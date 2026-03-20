---
name: stock-screener
description: "Screen stocks and backtest trading strategies using reportify-sdk (Python) and reportify-cli. Supports 80+ technical indicators (MA/EMA/MACD/KDJ/BOLL/RSI) and fundamental factors (PE/PB/ROE/ROA/EPS), time series analysis, multi-condition screening, and strategy backtesting. Use when user needs stock screening, filtering stocks by conditions, quantitative analysis, or backtesting trading strategies."
---

# Stock Screener & Backtesting

Screen stocks and backtest strategies using **Python SDK** (`reportify-sdk`) for full control, or **CLI** (`reportify-cli quant`) for quick queries.

> **Before screening, read the formula reference:** [references/formula.md](references/formula.md)
> **For real-world examples:** [references/scenarios.md](references/scenarios.md)

**Shortcuts** (if configured): `$quant` = `reportify-cli quant`, `$agent` = `reportify-cli agent`

**Install**: `pip3 install reportify-cli reportify-sdk` (SDK required for Python screening). For backtesting: `pip3 install backtrader pandas`

**API Key**: Get your key at https://reportify.cn/skills then set:
```bash
export REPORTIFY_API_KEY="your_api_key"
```

---

## Date Rules

- User specified exact date → use it
- User said "today" / "recent" / no date mentioned → dynamic calculation in Python:
  - Before 16:00 → yesterday (today's data may not be ready)
  - After 16:00 → today

## Result = 0 Rule

If screening returns 0 results, **tell the user directly** and stop. Do NOT modify formulas or relax conditions unless user explicitly asks.

---

## Python SDK Screening (Primary Method)

### Setup

```python
try:
    import reportify_sdk
except ImportError:
    import subprocess
    subprocess.check_call(["pip", "install", "reportify-sdk"])

from reportify_sdk import Reportify
client = Reportify(timeout=60)
```

### Workflow

> **Execution rule:** Write code to `.py` file first, then run `python3 script.py`. Do NOT use `python3 -c` or heredoc.

```python
import json
from datetime import datetime, timedelta
from reportify_sdk import Reportify

client = Reportify(timeout=60)

# Dynamic date: before 16:00 use yesterday, after use today
now = datetime.now()
check_date = (now - timedelta(days=1)).strftime("%Y-%m-%d") if now.hour < 16 else now.strftime("%Y-%m-%d")

# Step 1: Define conditions and their relationship
conditions = [
    {"name": "MA golden cross", "formula": "CROSS(MA(CLOSE, 5), MA(CLOSE, 20))", "file": "cond1.json"},
    {"name": "Volume surge", "formula": "VOLUME > MA(VOLUME, 5) * 2", "file": "cond2.json"},
]
relation = "intersection"  # intersection | union | difference

# Step 2: Execute each condition independently on full market
for cond in conditions:
    df = client.quant.factors_screen(formula=cond["formula"], market="cn", check_date=check_date)
    symbols = df["symbol"].tolist() if not df.empty else []
    details = df[["symbol", "name", "name_en", "close"]].to_dict("records") if not df.empty else []
    with open(cond["file"], "w", encoding="utf-8") as f:
        json.dump({"name": cond["name"], "formula": cond["formula"], "count": len(symbols), "symbols": symbols, "details": details}, f, ensure_ascii=False, indent=2)
    print(f"Done: {cond['name']}: {len(symbols)} stocks")

# Step 3: Read files, compute set operation
all_data = [json.load(open(c["file"], encoding="utf-8")) for c in conditions]
sets = [set(d["symbols"]) for d in all_data]
final = sets[0] & sets[1]  # intersection

# Step 4: Output results
stock_details = {}
for d in all_data:
    for item in d.get("details", []):
        stock_details[item["symbol"]] = item
results = [stock_details[s] for s in final if s in stock_details]
for item in results:
    print(f"{item['symbol']} - {item['name']} - {item['close']}")
```

### Core Principles

- **Each condition runs independently** on full market — never use previous results to narrow scope
- **Save each result to file** before computing intersections
- **Set operations after all files saved**: `&` (intersection), `|` (union), `-` (difference)

### SDK Methods

```python
# Screen stocks
df = client.quant.factors_screen(formula="(PE_TTM() < 20) & (ROE() > 0.15)", market="cn", check_date=check_date)

# Compute indicators
df = client.quant.indicators_compute(symbols=["000001"], formula="MACD()", market="cn", start_date="2026-01-01")

# Compute factors
df = client.quant.factors_compute(symbols=["000001"], formula="PE_TTM()", market="cn")

# Get OHLCV
df = client.quant.ohlcv("600519", market="cn", start_date="2025-01-01")
df = client.quant.ohlcv_batch(["000001", "600519"], market="cn")

# Company info
info = client.stock.overview(symbols="000001")
```

---

## CLI Screening (Quick Queries)

```bash
# Screen stocks
reportify-cli quantscreen --input '{"formula": "(PE_TTM() < 20) & (ROE() > 0.15)", "market": "cn"}' --format json

# Compute indicators
reportify-cli quantcompute_indicators --input '{"symbols": ["000001"], "formula": "RSI(14)", "start_date": "2025-01-01"}' --format json

# List available functions
reportify-cli quantlist_indicators --format table
reportify-cli quantlist_factors --format table
```

---

## Backtesting

### CLI Quick Backtest (`reportify-cli quantbacktest`)

```bash
reportify-cli quantbacktest --input '{"symbol": "000001", "start_date": "2024-01-01", "end_date": "2025-01-01", "entry_formula": "CROSS(MA(CLOSE, 5), MA(CLOSE, 20))", "exit_formula": "CROSSDOWN(MA(CLOSE, 5), MA(CLOSE, 20))", "initial_cash": 100000}' --format json
```

Params: `symbol` (required), `start_date` (required), `end_date` (required), `entry_formula` (required), `exit_formula`, `market`, `initial_cash`, `commission`, `stop_loss`, `sizer_percent`, `labels` (dict for extra indicators in output).

### Python Backtrader (Full Control)

```python
import backtrader as bt
import pandas as pd
from reportify_sdk import Reportify

client = Reportify()

# Data adapter
class ReportifyData(bt.feeds.PandasData):
    params = (('datetime', None), ('open', 'open'), ('high', 'high'),
              ('low', 'low'), ('close', 'close'), ('volume', 'volume'), ('openinterest', None))

def fetch_data(symbol, start_date, end_date):
    df = client.quant.ohlcv(symbol=symbol, start_date=start_date, end_date=end_date)
    if df is None or len(df) == 0: return None
    if isinstance(df, dict): df = pd.DataFrame(df)
    df = df.sort_values('date').reset_index(drop=True)
    df['date'] = pd.to_datetime(df['date'])
    df.set_index('date', inplace=True)
    return df

# Strategy
class MyStrategy(bt.Strategy):
    def __init__(self):
        self.macd = bt.indicators.MACD(self.data.close)

    def next(self):
        if not self.position:
            if self.macd.macd[-1] <= self.macd.signal[-1] and self.macd.macd[0] > self.macd.signal[0]:
                self.buy(size=100)
        else:
            if self.macd.macd[0] < self.macd.signal[0]:
                self.close()

# Run
cerebro = bt.Cerebro()
cerebro.broker.setcash(1_000_000)
cerebro.broker.setcommission(commission=0.0003)

df = fetch_data("600519", "2024-01-01", "2025-01-01")
if df is not None:
    cerebro.adddata(ReportifyData(dataname=df, name="600519"))

cerebro.addstrategy(MyStrategy)
cerebro.addanalyzer(bt.analyzers.Returns, _name='returns')
cerebro.addanalyzer(bt.analyzers.DrawDown, _name='drawdown')

results = cerebro.run()
print(f"Final: {cerebro.broker.getvalue():,.2f}")
print(f"Return: {(cerebro.broker.getvalue() / 1_000_000 - 1) * 100:.2f}%")
```

**Important:**
- A-shares commission: 0.0003 (万三)
- Buy in lots of 100: `int(cash / price / 100) * 100`
- No future functions — all signals based on current and past data

---

## Formula Quick Reference

### Variables
`CLOSE`/`C`, `OPEN`/`O`, `HIGH`/`H`, `LOW`/`L`, `VOLUME`/`V`, `CHG_PERCENT`, `TURNOVER_RATE`, `MARKET_CAP`, `CIRCULATING_MARKET_CAP`

### Market State Functions
`IS_LIMIT_UP()`, `IS_LIMIT_DOWN()`, `IS_ST()`, `IS_CHAIN_MAIN()`, `IS_CHAIN_STAR()`, `IS_CHAIN_CHINEXT()`

### Operators
**CRITICAL**: Both sides of `&`/`|` MUST be in parentheses: `(cond1) & (cond2)`

| Type | Operators |
|------|-----------|
| Comparison | `>`, `<`, `>=`, `<=`, `==`, `!=` |
| Logical | `&` / `AND`, `\|` / `OR`, `~` / `NOT` (case-insensitive) |
| Arithmetic | `+`, `-`, `*`, `/` |

### Core Functions (TongDaXin style: col first, period second)

**Moving Averages**: `MA(col,n)`, `EMA(col,n)`, `SMA(col,n,m)`, `DMA(col,a)`, `WMA(col,n)`
**Statistics**: `HHV(col,n)`, `LLV(col,n)`, `STD(col,n)`, `AVEDEV(col,n)`, `SUM(col,n)`
**Reference**: `REF(col,n)`, `DIFF(col,n)`
**Math**: `ABS(x)`, `MAX(a,b)`, `MIN(a,b)`, `SQRT(x)`, `POW(x,n)`, `LN(x)`, `IF(cond,a,b)`, `CONST(x)`
**Crossover**: `CROSS(a,b)`, `CROSSDOWN(a,b)`, `LONGCROSS(a,b,n)`
**Counting**: `EVERY(cond,n)`, `EXIST(cond,n)`, `COUNT(cond,n)`, `BARSLAST(cond)`, `LAST(cond,a,b)`
**Value**: `BETWEEN(x,a,b)`, `VALUEWHEN(cond,x)`

### Technical Indicators (Level 2, dot notation)

> **Important**: When nesting in REF/CROSS, must access field: `REF(RSI(14).rsi, 1)` ✅, `REF(RSI(14), 1)` ❌

**Trend**: `MACD().dif/.dea/.macd`, `BOLL().upper/.middle/.lower`, `EXPMA().ema1/.ema2`, `BBI().bbi`
**Momentum**: `KDJ().k/.d/.j`, `RSI(n).rsi`, `WR(n).wr/.wr1`, `CCI(n).cci`, `BIAS(n).bias1/.bias2/.bias3`, `MTM(n).mtm/.mtmma`, `ROC(n).roc/.maroc`, `PSY(n).psy/.psyma`
**Volatility**: `ATR(n).atr`, `DMI(n).pdi/.mdi/.adx/.adxr`, `VR(n).vr`, `OBV().obv`, `MFI(n).mfi`

### Fundamental Factors (must have parentheses)

**Valuation**: `PE()`, `PE_TTM()`, `PE_STATIC()`, `PE_DYNAMIC()`, `PB()`, `PS()`, `PCF()`, `EV()`, `EV_TO_EBIT()`, `PEG()`, `DIVIDEND_YIELD()`/`DY()`, `TOTAL_MARKET_VALUE()`/`TMV()`
**Profitability**: `ROE()`, `ROA()`, `GPM()`, `NPM()`, `OPERATING_PROFIT_MARGIN()`/`OPM()`, `ROIC()`
**Growth**: `REVENUE_GROWTH_RATE()`, `NET_PROFIT_GROWTH_RATE()`, `EPS_GROWTH_RATE()`, `OPERATING_CASH_FLOW_GROWTH_RATE()`, `NET_ASSET_GROWTH_RATE()`, `TOTAL_ASSET_GROWTH_RATE()`
**Solvency**: `CURRENT_RATIO()`, `QUICK_RATIO()`, `CASH_RATIO()`, `DEBT_RATIO()`, `DEBT_TO_EQUITY_RATIO()`/`DER()`, `EQUITY_MULTIPLIER()`/`EM()`
**Operating**: `ACCOUNTS_RECEIVABLE_TURNOVER()`, `ACCOUNTS_RECEIVABLE_DAYS()`/`DSO()`, `INVENTORY_TURNOVER()`, `INVENTORY_DAYS()`/`DIO()`, `CASH_CONVERSION_CYCLE()`/`CCC()`, `TOTAL_ASSET_TURNOVER()`
**Per-Share**: `EPS()`, `BPS()`, `OCFPS()`, `REVENUE_PER_SHARE()`, `DIVIDEND_PER_SHARE()`/`DPS()`, `CAPITAL_RESERVE_PER_SHARE()`

### Common Templates
```
# Moving averages
MA(CLOSE,5) > MA(CLOSE,20)                           # Short MA above long MA
CROSS(MA(CLOSE,5), MA(CLOSE,20))                     # Golden cross
(MA(CLOSE,5) > MA(CLOSE,10)) & (MA(CLOSE,10) > MA(CLOSE,20))  # Bull alignment

# K-line patterns
CLOSE > OPEN                                          # Bullish candle
(CLOSE >= REF(CLOSE, 1) * 1.095) & (HIGH > LOW)     # Non-limit-up board
VOLUME >= MA(VOLUME, 5) * 1.5                        # Volume surge

# Technical indicators
CROSS(MACD().dif, MACD().dea)                        # MACD golden cross
(KDJ().j < 20) & (CROSS(KDJ().k, KDJ().d))          # KDJ oversold golden cross
RSI(14) < 30                                          # RSI oversold

# Time series
EVERY(CLOSE > REF(CLOSE,1), 3)                       # 3 consecutive up days
EXIST(CROSS(MACD().dif, MACD().dea), 5)              # MACD cross within 5 days
CLOSE > HHV(HIGH, 20)                                # 20-day new high

# Fundamentals
(PE_TTM() < 20) & (ROE() > 0.15) & (PB() < 2)      # Value + quality
(REVENUE_GROWTH_RATE() > 0.3) & (NET_PROFIT_GROWTH_RATE() > 0.3)  # High growth

# Market state
~IS_ST() & (RSI(14) < 30)                            # Non-ST, oversold
IS_CHAIN_MAIN() & (VOLUME > MA(VOLUME, 5) * 2)      # Main board, volume surge
~IS_ST() & (CIRCULATING_MARKET_CAP < 5000000000)     # Non-ST, small cap < 50B
```

For complete formula reference: [references/formula.md](references/formula.md)
For screening scenarios: [references/scenarios.md](references/scenarios.md)

---

## Complex Screening → Agent

For screening logic beyond formula syntax (e.g., "非一字涨停", custom multi-step logic), delegate to Agent:

```bash
RESULT=$(reportify-cli agent create_conversation --input "{\"conversation_type\": \"bot_chat\", \"title\": \"用户的问题\"}")
CONV_ID=$(echo "$RESULT" | python3 -c "import sys,json; print(json.load(sys.stdin)['id'])")
reportify-cli agent chat --input "{\"conversation_id\": ${CONV_ID}, \"message\": \"用户的原始问题\", \"background\": true}"
```

## Limitations

Not currently supported:
- Capital flow data (large/small order net inflow)
- Bid-ask ratio (委比)
