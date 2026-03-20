# Formula Function Quick Reference

## Unified Function Style

**TongDaXin style: `func(col, n)`** **— col first, n second**

All window functions follow this style for easy memorization and use.

---

## Level 0 Core Functions

### Market Variables

| Variable                   | Alias        | Description                          |
| ------------------------ | ---------- | ---------------------------------- |
| `CLOSE`                  | `C`        | Closing price                        |
| `OPEN`                   | `O`        | Opening price                        |
| `HIGH`                   | `H`        | Highest price                        |
| `LOW`                    | `L`        | Lowest price                         |
| `VOLUME`                 | `V`, `VOL` | Trading volume                       |
| `CHG_PERCENT`            | -          | Price change ratio (5% = 0.05)       |
| `TURNOVER_RATE`          | -          | Turnover rate (5% = 0.05)           |
| `MARKET_CAP`             | -          | Total market capitalization          |
| `CIRCULATING_MARKET_CAP` | -          | Free-float market capitalization     |

### Market Status Functions

| Function               | Description              | Example                     |
| -------------------- | ---------------------- | ------------------------- |
| `IS_LIMIT_UP()`      | Limit-up (one-line)      | `IS_LIMIT_UP() == 1`      |
| `IS_LIMIT_DOWN()`    | Limit-down (one-line)    | `IS_LIMIT_DOWN() == 1`    |
| `IS_ST()`            | Whether ST stock         | `IS_ST() == 0` exclude ST  |
| `IS_CHAIN_MAIN()`    | Whether Main Board stock | `IS_CHAIN_MAIN() == 1`    |
| `IS_CHAIN_STAR()`    | Whether STAR Market stock | `IS_CHAIN_STAR() == 1`    |
| `IS_CHAIN_CHINEXT()` | Whether ChiNext stock    | `IS_CHAIN_CHINEXT() == 1` |

### Moving Average Functions

| Function             | Parameters                          | Description                  | Example                              |
| ------------------ | --------------------------------- | -------------------------- | ---------------------------------- |
| `MA(col, n)`       | col: column, n: period              | Simple moving average        | `MA(CLOSE, 20)`, `MA(VOLUME, 5)`   |
| `EMA(col, n)`      | col: column, n: period              | Exponential moving average   | `EMA(CLOSE, 12)`, `EMA(CLOSE, 26)` |
| `SMA(col, n, m=1)` | col: column, n: period, m: weight   | Chinese-style weighted average | `SMA(CLOSE, 12, 1)`                |
| `WMA(col, n)`      | col: column, n: period              | Linear weighted average      | `WMA(CLOSE, 10)`                   |
| `DMA(col, alpha)`  | col: column, alpha: smoothing factor | Dynamic moving average       | `DMA(CLOSE, 0.1)`                  |

### Statistical Functions

| Function           | Parameters                  | Description              | Example                                                  |
| ---------------- | ------------------------- | ---------------------- | ------------------------------------------------------ |
| `HHV(col, n)`    | col: column, n: period      | Highest value in N days  | `HHV(HIGH, 20)`, `HHV(CLOSE, 10)`                      |
| `LLV(col, n)`    | col: column, n: period      | Lowest value in N days   | `LLV(LOW, 20)`, `LLV(CLOSE, 10)`                       |
| `STD(col, n)`    | col: column, n: period      | Standard deviation over N days | `STD(CLOSE, 20)`                                  |
| `SUM(col, n)`    | col: column, n: period      | Rolling sum over N days  | `SUM(VOLUME, 5)`, `SUM(VOLUME, 0)` cumulative           |
| `AVEDEV(col, n)` | col: column, n: period      | Mean absolute deviation  | `AVEDEV(CLOSE, 14)`                                     |

### Reference Functions

| Function          | Parameters                  | Description                        | Example                                                |
| --------------- | ------------------------- | -------------------------------- | ---------------------------------------------------- |
| `REF(col, n)`   | col: column, n: period      | Value N days ago                   | `REF(CLOSE, 1)` previous close, `REF(VOLUME, 5)`     |
| `DIFF(col, n)`  | col: column, n: period      | Difference from N days ago         | `DIFF(CLOSE, 1)`                                      |
| `CONST(col)`    | col: column                 | Returns constant series of last value | `CONST(CLOSE)` all rows equal to last closing price |
| `RET(col, n=1)` | col: column, n: position    | Returns the N-th value from the end | `RET(CLOSE, 1)` last value                           |

### Math Functions

| Function                          | Description          | Example                                |
| ------------------------------- | -------------------- | ------------------------------------ |
| `ABS(x)`                        | Absolute value         | `ABS(CLOSE - OPEN)`                  |
| `MAX(a, b)`                     | Maximum of two values  | `MAX(HIGH - LOW, ABS(CLOSE - OPEN))` |
| `MIN(a, b)`                     | Minimum of two values  | `MIN(CLOSE, OPEN)`                   |
| `IF(cond, true_val, false_val)` | Conditional expression | `IF(CLOSE > OPEN, 1, 0)`             |
| `LN(x)`                         | Natural logarithm      | `LN(CLOSE)`                          |
| `POW(x, n)`                     | Power                  | `POW(CLOSE, 2)`                      |
| `SQRT(x)`                       | Square root            | `SQRT(CLOSE)`                        |
| `SIN(x)`                        | Sine (radians)         | `SIN(x)`                             |
| `COS(x)`                        | Cosine (radians)       | `COS(x)`                             |
| `TAN(x)`                        | Tangent (radians)      | `TAN(x)`                             |
| `RD(x, n=3)`                    | Round to N decimals    | `RD(CLOSE, 2)`                       |

---

## Level 1 Application Functions

### Crossover Functions

| Function                     | Parameters                            | Description          | Return | Example                                       |
| -------------------------- | ----------------------------------- | -------------------- | ------ | ------------------------------------------- |
| `CROSS(fast, slow)`        | fast: fast line, slow: slow line      | Golden cross (up)      | 0/1    | `CROSS(MA(CLOSE, 5), MA(CLOSE, 20))`        |
| `CROSSDOWN(fast, slow)`    | fast: fast line, slow: slow line      | Death cross (down)     | 0/1    | `CROSSDOWN(MA(CLOSE, 5), MA(CLOSE, 20))`    |
| `LONGCROSS(fast, slow, n)` | fast, slow, n: duration period        | Long-term crossover    | 0/1    | `LONGCROSS(MA(CLOSE, 5), MA(CLOSE, 20), 5)` |

### Counting Functions

| Function             | Parameters                        | Description                                  | Return | Example                                          |
| ------------------ | ------------------------------- | ------------------------------------------ | ------ | ---------------------------------------------- |
| `COUNT(cond, n)`   | cond: condition, n: period        | Count of days condition is true in N days    | int    | `COUNT(CLOSE > OPEN, 10)`                      |
| `EVERY(cond, n)`   | cond: condition, n: period        | Condition is true for all N days             | 0/1    | `EVERY(CLOSE > REF(CLOSE, 1), 3)`              |
| `EXIST(cond, n)`   | cond: condition, n: period        | Condition was true at least once in N days   | 0/1    | `EXIST(LOW <= BOLL().lower, 10)`               |
| `BARSLAST(cond)`   | cond: condition                   | Days since condition was last true           | int    | `BARSLAST(CROSS(MA(CLOSE, 5), MA(CLOSE, 20)))` |
| `LAST(cond, a, b)` | cond, a, b: period range          | Condition held continuously in \[a, b] range | 0/1    | `LAST(CLOSE > OPEN, 5, 1)`                     |

### Value Retrieval Functions

| Function                    | Parameters                        | Description                       | Example                                                  |
| ------------------------- | ------------------------------- | ------------------------------- | ------------------------------------------------------ |
| `VALUEWHEN(cond, val)`    | cond: condition, val: value       | Retrieve value when condition met | `VALUEWHEN(CROSS(MA(CLOSE, 5), MA(CLOSE, 20)), CLOSE)` |
| `BETWEEN(val, low, high)` | val, low, high                    | Whether value is within range     | `BETWEEN(RSI(14), 30, 70)`                             |

---

## Level 2 Technical Indicators

> **Important rule:** Level 2 indicators return multi-field objects.
>
> - **Simple comparisons** with single-field indicators can auto-resolve: `RSI(14) < 30` ✅
> - **When passing to other functions**, fields must be explicitly accessed: `REF(RSI(14).rsi, 1)` ✅
> - **Incorrect usage**: `REF(RSI(14), 1)` ❌ will throw "No field 'shift'"

### Trend Indicators

| Indicator                       | Parameters                          | Output Fields                | Example                         |
| ------------------------------- | ----------------------------------- | -------------------------- | ----------------------------- |
| `MACD(short=12, long=26, m=9)`  | short period, long period, signal line | `dif`, `dea`, `macd`       | `MACD().dif > MACD().dea`     |
| `BOLL(n=20, p=2)`               | period, std dev multiplier            | `upper`, `middle`, `lower` | `CLOSE > BOLL().upper`        |
| `TAQ(n=20)`                     | period (Donchian Channel)             | `up`, `mid`, `down`        | `CLOSE > TAQ().up`            |
| `KTN(n=20, m=10)`               | EMA period, ATR period                | `upper`, `mid`, `lower`    | `CLOSE > KTN().upper`         |
| `EXPMA(n1=12, n2=50)`           | short period, long period             | `ema1`, `ema2`             | `EXPMA().ema1 > EXPMA().ema2` |
| `BBI(m1=3, m2=6, m3=12, m4=20)` | 4 MA periods                         | `bbi`                      | `CLOSE > BBI().bbi`           |
| `XSII(n=102, m=7)`              | TD1/TD2 factor, TD3/TD4 factor       | `td1`, `td2`, `td3`, `td4` | `CLOSE > XSII().td3`          |

### Momentum Indicators

| Indicator                    | Parameters                          | Output Fields               | Example                                     |
| -------------------------- | --------------------------------- | ------------------------- | ----------------------------------------- |
| `KDJ(n=9, m1=3, m2=3)`     | RSV period, K smooth, D smooth      | `k`, `d`, `j`             | `KDJ().j < 20`, `CROSS(KDJ().k, KDJ().d)` |
| `RSI(n=24)`                | period                               | `rsi`                     | `RSI(14) < 30`                            |
| `WR(n=10, n1=6)`           | two periods                          | `wr`, `wr1`               | `WR().wr < 20`                            |
| `CCI(n=14)`                | period                               | `cci`                     | `CCI(14).cci > 100`                       |
| `BIAS(l1=6, l2=12, l3=24)` | three periods                       | `bias1`, `bias2`, `bias3` | `BIAS().bias1 < -5`                       |
| `PSY(n=12, m=6)`           | PSY period, MA period                | `psy`, `psyma`            | `PSY().psy < 25`                          |
| `MTM(n=12, m=6)`           | MTM period, MA period                | `mtm`, `mtmma`            | `MTM().mtm > 0`                           |
| `ROC(n=12, m=6)`           | ROC period, MA period                | `roc`, `maroc`            | `ROC().roc > 0`                           |

### Volatility Indicators

| Indicator              | Parameters              | Output Fields                 | Example                             |
| -------------------- | --------------------- | --------------------------- | --------------------------------- |
| `ATR(n=20)`          | period                  | `atr`                       | `ATR(14).atr / CLOSE < 0.02`      |
| `DMI(m1=14, m2=6)`   | DI period, ADX period   | `pdi`, `mdi`, `adx`, `adxr` | `DMI().pdi > DMI().mdi`           |
| `TRIX(m1=12, m2=20)` | EMA period, MA period   | `trix`, `trma`              | `CROSS(TRIX().trix, TRIX().trma)` |
| `VR(m1=26)`          | period                  | `vr`                        | `VR().vr > 200`                   |
| `OBV()`              | none                    | `obv`                       | `OBV().obv > REF(OBV().obv, 5)`   |

### Volume Indicators

| Indicator     | Parameters | Output Field | Example                           |
| ----------- | -------- | ---------- | ------------------------------- |
| `MFI(n=14)` | period     | `mfi`      | `MFI().mfi < 20`                |
| `OBV()`     | none       | `obv`      | `OBV().obv > REF(OBV().obv, 5)` |
| `VR(m1=26)` | period     | `vr`       | `VR().vr > 200`                 |

### Other Technical Indicators

| Indicator                    | Parameters                             | Output Fields       | Description              |
| -------------------------- | ------------------------------------- | ----------------- | ---------------------- |
| `CR(n=20)`                 | period                                  | `cr`              | Price momentum indicator |
| `EMV(n=14, m=9)`           | EMV period, MA period                   | `emv`, `maemv`    | Ease of movement         |
| `DPO(m1=20, m2=10, m3=6)`  | MA period, REF period, MADPO period     | `dpo`, `madpo`    | Detrended price oscillator |
| `BRAR(m1=26)`              | period                                  | `ar`, `br`        | Sentiment indicator      |
| `DFMA(n1=10, n2=50, m=10)` | short MA period, long MA period, DIFMA period | `dif`, `difma` | Parallel line difference |
| `MASS(n1=9, n2=25, m=6)`   | MA period, SUM period, MA\_MASS period  | `mass`, `ma_mass` | Mass index               |
| `ASI(m1=26, m2=10)`        | ASI period, ASIT period                 | `asi`, `asit`     | Accumulation swing index |

---

## Level 2 Fundamental Factors

**All fundamental factors are functions and must be called with parentheses**

### Valuation Factors

| Factor                             | Description              | Common Usage                                    |
| -------------------------------- | ---------------------- | --------------------------------------------- |
| `PE()`                           | Price-to-earnings ratio  | `PE() < 20` undervalued                         |
| `PE_TTM()`                       | P/E ratio (trailing TTM) | `PE_TTM() < 15` undervalued                    |
| `PE_STATIC()`                    | P/E ratio (static)       | `PE_STATIC() < 20`                             |
| `PE_DYNAMIC()`                   | P/E ratio (dynamic)      | `PE_DYNAMIC() < 20`                            |
| `PB()`                           | Price-to-book ratio      | `PB() < 1` below book value, `PB() < 2` undervalued |
| `PS()`                           | Price-to-sales ratio     | `PS() < 2` undervalued                         |
| `PCF()`                          | Price-to-cash-flow ratio | `PCF() > 0` healthy cash flow                  |
| `EV()`                           | Enterprise value         | Used for EV/EBITDA calculation                  |
| `EV_TO_EBIT()`                   | Enterprise value multiple | `EV_TO_EBIT() < 10`                           |
| `PEG()`                          | PEG ratio                | `PEG() < 1` growth undervalued                 |
| `DY()` / `DIVIDEND_YIELD()`      | Dividend yield           | `DY() > 0.03` high dividend                    |
| `TOTAL_MARKET_VALUE()` / `TMV()` | Total market cap         | `TMV() > 10000000000` over 10B market cap       |

### Profitability

| Factor                                  | Description                    | Common Usage                                 |
| ------------------------------------- | ---------------------------- | ------------------------------------------ |
| `ROE()`                               | Return on equity               | `ROE() > 0.15` quality company               |
| `ROA()`                               | Return on assets               | `ROA() > 0.08` high asset utilization        |
| `GPM()`                               | Gross profit margin            | `GPM() > 0.3` strong product competitiveness |
| `NPM()`                               | Net profit margin              | `NPM() > 0.1` strong profitability           |
| `OPM()` / `OPERATING_PROFIT_MARGIN()` | Operating profit margin        | `OPM() > 0.15`                               |
| `ROIC()`                              | Return on invested capital     | `ROIC() > 0.1` high capital efficiency        |
| `COST_EXPENSE_RATIO()`                | Cost-expense profit ratio      | `COST_EXPENSE_RATIO() > 0.1`                |
| `CASH_EARNINGS_COVERAGE_RATIO()`      | Cash earnings coverage ratio   | `CASH_EARNINGS_COVERAGE_RATIO() > 1`        |

### Growth Capability

| Factor                                | Description                              | Common Usage                                    |
| ----------------------------------- | -------------------------------------- | --------------------------------------------- |
| `REVENUE_GROWTH_RATE()`             | Revenue growth rate (YoY)                | `REVENUE_GROWTH_RATE() > 0.2` high growth       |
| `NET_PROFIT_GROWTH_RATE()`          | Net profit growth rate (YoY)             | `NET_PROFIT_GROWTH_RATE() > 0.3` high growth    |
| `EPS_GROWTH_RATE()`                 | EPS growth rate (YoY)                    | `EPS_GROWTH_RATE() > 0.2` earnings growth       |
| `OPERATING_CASH_FLOW_GROWTH_RATE()` | Operating cash flow growth rate (YoY)    | `OPERATING_CASH_FLOW_GROWTH_RATE() > 0.2`       |
| `NET_ASSET_GROWTH_RATE()`           | Net asset growth rate (YoY)              | `NET_ASSET_GROWTH_RATE() > 0.1`                 |
| `TOTAL_ASSET_GROWTH_RATE()`         | Total asset growth rate (YoY)            | `TOTAL_ASSET_GROWTH_RATE() > 0.1`               |

### Solvency

| Factor                                  | Description                        | Common Usage                                 |
| ------------------------------------- | -------------------------------- | ------------------------------------------ |
| `CURRENT_RATIO()`                     | Current ratio                      | `CURRENT_RATIO() > 2` sufficient liquidity   |
| `QUICK_RATIO()`                       | Quick ratio                        | `QUICK_RATIO() > 1` strong short-term solvency |
| `CASH_RATIO()`                        | Cash ratio                         | `CASH_RATIO() > 0.2`                        |
| `CASH_FLOW_RATIO()`                   | Cash flow ratio                    | `CASH_FLOW_RATIO() > 0.5`                   |
| `DEBT_RATIO()`                        | Debt-to-asset ratio                | `DEBT_RATIO() < 0.5` low leverage            |
| `DEBT_TO_EQUITY_RATIO()` / `DER()`    | Debt-to-equity ratio               | `DEBT_TO_EQUITY_RATIO() < 1` financially sound |
| `EQUITY_MULTIPLIER()` / `EM()`        | Equity multiplier                  | `EQUITY_MULTIPLIER() < 3`                   |
| `INTEREST_COVERAGE_RATIO()` / `ICR()` | Interest coverage ratio            | `ICR() > 3`                                 |
| `CASH_FLOW_INTEREST_COVERAGE()`       | Cash flow interest coverage ratio  | `CASH_FLOW_INTEREST_COVERAGE() > 3`         |
| `LONG_TERM_CAPITAL_DEBT_RATIO()`      | Long-term capital debt ratio       | `LONG_TERM_CAPITAL_DEBT_RATIO() < 0.5`      |

### Per-Share Metrics

| Factor                               | Description                    | Common Usage                                 |
| ---------------------------------- | ---------------------------- | ------------------------------------------ |
| `EPS()`                            | Earnings per share             | `EPS() > 1` strong profitability              |
| `BPS()`                            | Book value per share           | `BPS() > 10` strong asset base                |
| `OCFPS()`                          | Operating cash flow per share  | `OCFPS() > 0` healthy cash flow               |
| `REVENUE_PER_SHARE()`              | Revenue per share              | `REVENUE_PER_SHARE() > 10`                    |
| `CAPITAL_RESERVE_PER_SHARE()`      | Capital reserve per share      | `CAPITAL_RESERVE_PER_SHARE() > 2`             |
| `UNDISTRIBUTED_PROFIT_PER_SHARE()` | Undistributed profit per share | `UNDISTRIBUTED_PROFIT_PER_SHARE() > 2`        |
| `DPS()` / `DIVIDEND_PER_SHARE()`   | Dividend per share             | `DPS() > 0.5`                                |

### Operational Efficiency

| Factor                                   | Description                       | Common Usage                                        |
| -------------------------------------- | ------------------------------- | ------------------------------------------------- |
| `INVENTORY_TURNOVER()`                 | Inventory turnover ratio          | `INVENTORY_TURNOVER() > 5` good inventory management |
| `INVENTORY_DAYS()` / `DIO()`           | Days inventory outstanding        | `INVENTORY_DAYS() < 60`                             |
| `TOTAL_ASSET_TURNOVER()`               | Total asset turnover ratio        | `TOTAL_ASSET_TURNOVER() > 0.5` high asset utilization |
| `FIXED_ASSET_TURNOVER()`               | Fixed asset turnover ratio        | `FIXED_ASSET_TURNOVER() > 1`                        |
| `CURRENT_ASSET_TURNOVER()`             | Current asset turnover ratio      | `CURRENT_ASSET_TURNOVER() > 1`                      |
| `ACCOUNTS_RECEIVABLE_TURNOVER()`       | Accounts receivable turnover      | `ACCOUNTS_RECEIVABLE_TURNOVER() > 6`                |
| `ACCOUNTS_RECEIVABLE_DAYS()` / `DSO()` | Days sales outstanding            | `ACCOUNTS_RECEIVABLE_DAYS() < 60` strong collections |
| `ACCOUNTS_PAYABLE_TURNOVER()`          | Accounts payable turnover         | `ACCOUNTS_PAYABLE_TURNOVER() > 5`                   |
| `ACCOUNTS_PAYABLE_DAYS()`              | Days payable outstanding          | `ACCOUNTS_PAYABLE_DAYS() < 90`                      |
| `CASH_CONVERSION_CYCLE()` / `CCC()`    | Cash conversion cycle             | `CCC() < 60`                                        |

### Financial Statement Fields (Direct Access)

#### Income Statement Fields (INCOME.\*)

| Field                                                    | Description                              |
| -------------------------------------------------------- | -------------------------------------- |
| `INCOME.total_revenue`                                   | Total revenue                            |
| `INCOME.operating_revenue`                               | Operating revenue                        |
| `INCOME.total_operating_costs`                           | Total operating costs                    |
| `INCOME.operating_costs`                                 | Operating costs                          |
| `INCOME.selling_expenses`                                | Selling expenses                         |
| `INCOME.general_and_administrative_expenses`             | General and administrative expenses      |
| `INCOME.research_and_development_expense`                | Research and development expenses        |
| `INCOME.financial_expenses`                              | Financial expenses                       |
| `INCOME.interest_expense`                                | Interest expense                         |
| `INCOME.operating_profit`                                | Operating profit                         |
| `INCOME.net_profit`                                      | Net profit                               |
| `INCOME.net_profit_attributable_to_owners_of_the_parent` | Net profit attributable to parent company |
| `INCOME.basic_earnings_per_share`                        | Basic earnings per share                 |
| `INCOME.diluted_earnings_per_share`                      | Diluted earnings per share               |

#### Balance Sheet Fields (BALANCESHEET.\*)

| Field                                    | Description                 |
| ---------------------------------------- | ------------------------- |
| `BALANCESHEET.cash_and_cash_equivalents` | Cash and cash equivalents   |
| `BALANCESHEET.accounts_receivable`       | Accounts receivable         |
| `BALANCESHEET.inventories`               | Inventories                 |
| `BALANCESHEET.total_current_assets`      | Total current assets        |
| `BALANCESHEET.fixed_assets`              | Fixed assets                |
| `BALANCESHEET.total_assets`              | Total assets                |
| `BALANCESHEET.short_term_borrowings`     | Short-term borrowings       |
| `BALANCESHEET.total_current_liabilities` | Total current liabilities   |
| `BALANCESHEET.long_term_borrowings`      | Long-term borrowings        |
| `BALANCESHEET.total_liabilities`         | Total liabilities           |
| `BALANCESHEET.paid_in_capital`           | Paid-in capital (shares)    |
| `BALANCESHEET.capital_reserve`           | Capital reserve             |
| `BALANCESHEET.retained_earnings`         | Retained earnings           |
| `BALANCESHEET.total_shareholders_equity` | Total shareholders' equity  |

#### Cash Flow Statement Fields (CASHFLOW.\*)

| Field                                                                                  | Description                                                    |
| -------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| `CASHFLOW.net_cash_flows_from_operating_activities`                                    | Net cash flows from operating activities                       |
| `CASHFLOW.net_cash_flows_from_investing_activities`                                    | Net cash flows from investing activities                       |
| `CASHFLOW.net_cash_flows_from_financing_activities`                                    | Net cash flows from financing activities                       |
| `CASHFLOW.cash_payments_for_distribution_of_dividends_or_profit_and_interest_expenses` | Cash payments for dividends, profit distribution, or interest  |

#### Equity Structure Fields (EQUITY.\*)

| Field                             | Description               |
| --------------------------------- | ----------------------- |
| `EQUITY.total_share_capital`      | Total share capital       |
| `EQUITY.total_outstanding_shares` | Total outstanding shares  |
| `EQUITY.outstanding_a_shares`     | Outstanding A-shares      |
| `EQUITY.restricted_a_shares`      | Restricted A-shares       |

---

## Operators

| Type       | Operators                               | Description                                                         |
| ---------- | ------------------------------------- | ----------------------------------------------------------------- |
| Comparison | `>`, `<`, `>=`, `<=`, `==`, `!=`      | Comparison operators                                                |
| Logical    | `&` or `AND`, `\|` or `OR`, `~` or `NOT` | Case insensitive, **expressions on both sides must be wrapped in** **`()`** |
| Arithmetic | `+`, `-`, `*`, `/`                    | Arithmetic operators                                                |

---

## Common Screening Examples

### MACD Golden Cross + DIFF Filter

```python
MACD().dif > MACD().dea & (MACD().dif > 0) & (MACD().dif < 0.9)
```

### KDJ Oversold + RSI Bottom Divergence

```python
KDJ().j < 20 & RSI(14) < 30
```

### Undervalued + High Growth

```python
PE_TTM() < 15 & ROE() > 0.15 & NET_PROFIT_GROWTH_RATE() > 0.2
```

### Below Book Value + High Dividend

```python
PB() < 1 & DY() > 0.05
```

### Small Cap + High Turnover

```python
CIRCULATING_MARKET_CAP < 5000000000 & TURNOVER_RATE > 0.05
```

### Exclude ST and Limit-Up/Down Stocks

```python
IS_ST() == 0 & IS_LIMIT_UP() == 0 & IS_LIMIT_DOWN() == 0
```
