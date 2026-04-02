# Stock Screening Scenario Examples

This document provides detailed scenario examples showing how to convert user requirements into formulas and call MCP tools.

> **Multi-step screening best practices:**
> 1. **Run only one condition at a time**, saving results to variables (e.g., `result_1`, `result_2`)
> 2. **After all conditions are executed**, use a Python script to compute intersections/unions
> 3. **Get stock names**: `company_overview(symbols=final_list, market="cn")`
> 4. **Show statistics before output**: list each condition's formula and hit count
>
> See [SKILL.md](../SKILL.md) Section 4 "Multi-step Screening Workflow" for details

---

## Scenario 1: Bullish Candlestick Breaking Above Moving Average

**User requirement**: On 2026.1.22, a bullish real-body candlestick breaks above the 20-day moving average

### Condition Breakdown

1. **Bullish real-body candlestick**: Close > Open
2. **Breaking above 20-day MA**: Close > 20-day moving average

### Formula Construction

```python
(CLOSE > OPEN) & (CLOSE > MA(CLOSE, 20))
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(CLOSE > OPEN) & (CLOSE > MA(CLOSE, 20))",
    check_date="2026-01-22"
)
```

---

## Scenario 2: Bullish MA Alignment + Increasing Volume + MACD Golden Cross

**User requirement**:
- 5-day MA > 10-day MA > 20-day MA (bullish moving average alignment)
- Consecutive 3-day increasing volume from 2026.1.21 to 2026.1.23
- MACD golden cross formed on 1.20

### Condition Breakdown

1. **Bullish MA alignment**: `MA(CLOSE, 5) > MA(CLOSE, 10) > MA(CLOSE, 20)`
2. **Increasing volume**: 3 consecutive days of `VOLUME > REF(VOLUME, 1)`
3. **MACD golden cross**: `CROSS(MACD().dif, MACD().dea)`

### Multi-step Screening Strategy

Since conditions involve different dates, step-by-step processing is required.

**Step 1: Check bullish MA alignment (1.21-1.23)**

```python
factors_screen(
    market="cn",
    formula="(MA(CLOSE, 5) > MA(CLOSE, 10)) & (MA(CLOSE, 10) > MA(CLOSE, 20))",
    check_date="2026-01-23"
)
```

**Step 2: Check increasing volume on the result set**

```python
factors_screen(
    market="cn",
    formula="EVERY(VOLUME > REF(VOLUME, 1), 3)",
    check_date="2026-01-23",
    symbols=step1_results
)
```

**Step 3: Check MACD golden cross history**

```python
factors_compute(
    market="cn",
    formula="CROSS(MACD().dif, MACD().dea)",
    symbols=step2_results,
    start_date="2026-01-18",
    end_date="2026-01-23"
)
# Filter stocks where factor_value=True on 1.20
```

---

## Scenario 3: Non-one-line Limit Up + Turnover Rate

**User requirement**:
- Non-one-line limit up on 2026.1.22
- Turnover rate >= 8% on that day

### Condition Breakdown

1. **Limit up**: Price increase >= 9.5% (A-share limit up)
2. **Non-one-line**: High != Low
3. **Turnover rate**: Requires additional calculation (Volume / Free-float shares)

### Formula Construction

```python
# Non-one-line limit up condition
(CLOSE >= REF(CLOSE, 1) * 1.095) & (HIGH > LOW)
```

### MCP Call

```python
# Step 1: Screen for non-one-line limit up
factors_screen(
    market="cn",
    formula="(CLOSE >= REF(CLOSE, 1) * 1.095) & (HIGH > LOW)",
    check_date="2026-01-22"
)

# Step 2: Turnover rate requires additional processing
# Fetch OHLCV data and free-float share data, then calculate and filter by turnover rate
```

### Notes

Turnover rate = Volume / Free-float shares. The current formula system does not directly support this. You need to:
1. Use `ohlcv_batch` to get volume data
2. Obtain free-float share data through other APIs
3. Calculate turnover rate in code and then filter

---

## Scenario 4: Volume Contraction Consolidation

**User requirement**:
- From 2026.1.18 to 2026.1.22, stock price above the 5-day moving average
- Volume contracted by 70%-80% (i.e., current volume is only 20%-30% of average volume)

### Condition Breakdown

1. **Price above MA**: 5 consecutive days of `CLOSE > MA(CLOSE, 5)`
2. **Volume contraction**: Volume < 30% of 5-day average volume

### Formula Construction

```python
EVERY(CLOSE > MA(CLOSE, 5), 5) & (VOLUME < MA(VOLUME, 5) * 0.3)
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="EVERY(CLOSE > MA(CLOSE, 5), 5) & (VOLUME < MA(VOLUME, 5) * 0.3)",
    check_date="2026-01-22"
)
```

---

## Scenario 5: Bollinger Band Lower Band Rebound to Middle Band + MA Breakout

**User requirement**:
- Over 10 trading days from 2026.1.12 to 2026.1.23, stock price rebounded from Bollinger lower band to middle band
- Broke above 5-day and 10-day MA resistance
- Turnover rate > 2% for 10 consecutive trading days

### Condition Breakdown

1. **Currently at or above middle band**: `CLOSE >= BOLL().middle`
2. **Touched lower band within 10 days**: `EXIST(LOW <= BOLL().lower, 10)`
3. **MA breakout**: `CLOSE > MA(CLOSE, 5) & CLOSE > MA(CLOSE, 10)`
4. **Turnover rate condition**: Requires additional processing

### Formula Construction

```python
(CLOSE >= BOLL().middle) & (EXIST(LOW <= BOLL().lower, 10)) & (CLOSE > MA(CLOSE, 5)) & (CLOSE > MA(CLOSE, 10))
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(CLOSE >= BOLL().middle) & (EXIST(LOW <= BOLL().lower, 10)) & (CLOSE > MA(CLOSE, 5)) & (CLOSE > MA(CLOSE, 10))",
    check_date="2026-01-23"
)
```

### Notes

The turnover rate condition requires additional processing. See the explanation in Scenario 3.

---

## Scenario 6: Volume Surge + KDJ Golden Cross

**User requirement**:
- On January 23, 2026, daily volume >= 1.5x the 5-day average volume
- KDJ golden cross appeared

### Condition Breakdown

1. **Volume surge**: `VOLUME >= MA(VOLUME, 5) * 1.5`
2. **KDJ golden cross**: `CROSS(KDJ().k, KDJ().d)`

### Formula Construction

```python
(VOLUME >= MA(VOLUME, 5) * 1.5) & (CROSS(KDJ().k, KDJ().d))
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(VOLUME >= MA(VOLUME, 5) * 1.5) & (CROSS(KDJ().k, KDJ().d))",
    check_date="2026-01-23"
)
```

---

## More Strategy Templates

### Breakout Strategies

```python
# MA breakout
CLOSE > MA(CLOSE, 20)

# Bollinger upper band breakout
CLOSE > BOLL().upper

# N-day new high
CLOSE > HHV(HIGH, 20)

# Breaking previous high
CLOSE > REF(HHV(HIGH, 60), 1)
```

### Golden Cross Strategies

```python
# MA golden cross
CROSS(MA(CLOSE, 5), MA(CLOSE, 20))

# MACD golden cross
CROSS(MACD().dif, MACD().dea)

# KDJ golden cross
CROSS(KDJ().k, KDJ().d)

# KDJ low-level golden cross
(KDJ().j < 20) & (CROSS(KDJ().k, KDJ().d))
```

### Trend Strategies

```python
# Bullish MA alignment
(MA(CLOSE, 5) > MA(CLOSE, 10)) & (MA(CLOSE, 10) > MA(CLOSE, 20)) & (MA(CLOSE, 20) > MA(CLOSE, 60))

# Consecutive rallies
EVERY(CLOSE > REF(CLOSE, 1), 5)

# MA upward divergence
(MA(CLOSE, 5) > REF(MA(CLOSE, 5), 5)) & (MA(CLOSE, 20) > REF(MA(CLOSE, 20), 5))
```

### Overbought/Oversold Strategies

```python
# RSI oversold
RSI(14) < 30

# RSI overbought
RSI(14) > 70

# KDJ oversold
KDJ().j < 0

# Bollinger lower band touch
LOW <= BOLL().lower
```

### Volume-Price Strategies

```python
# Volume surge with price rise
(CLOSE > REF(CLOSE, 1)) & (VOLUME > MA(VOLUME, 5) * 1.5)

# Volume contraction consolidation
(CLOSE > MA(CLOSE, 5)) & (VOLUME < MA(VOLUME, 5) * 0.5)

# Volume and price rising together
EVERY((CLOSE > REF(CLOSE, 1)) & (VOLUME > REF(VOLUME, 1)), 3)
```

---

## Fundamental Screening Scenarios

### Scenario 7: Undervalued Value Stocks

**User requirement**: Screen for value stocks with PE below 15, PB below 2, and ROE above 12%

### Condition Breakdown

1. **Low PE**: `PE_TTM() < 15`
2. **Low PB**: `PB() < 2`
3. **High ROE**: `ROE() > 0.12`

### Formula Construction

```python
(PE_TTM() < 15) & (PB() < 2) & (ROE() > 0.12)
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(PE_TTM() < 15) & (PB() < 2) & (ROE() > 0.12)",
    check_date="2026-01-23"
)
```

---

### Scenario 8: High-Growth Stocks

**User requirement**: Screen for high-growth companies with revenue growth rate > 30% and net profit growth rate > 50%

### Condition Breakdown

1. **High revenue growth**: `REVENUE_GROWTH_RATE() > 0.3`
2. **High net profit growth**: `NET_PROFIT_GROWTH_RATE() > 0.5`

### Formula Construction

```python
(REVENUE_GROWTH_RATE() > 0.3) & (NET_PROFIT_GROWTH_RATE() > 0.5)
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(REVENUE_GROWTH_RATE() > 0.3) & (NET_PROFIT_GROWTH_RATE() > 0.5)",
    check_date="2026-01-23"
)
```

---

### Scenario 9: Below-Book-Value Stock Screening

**User requirement**: Screen for stocks with PB < 1 (below book value), but ROE > 5% and current ratio > 1.5 -- financially sound below-book-value stocks

### Condition Breakdown

1. **Below book value**: `PB() < 1`
2. **Still profitable**: `ROE() > 0.05`
3. **Adequate liquidity**: `CURRENT_RATIO() > 1.5`

### Formula Construction

```python
(PB() < 1) & (ROE() > 0.05) & (CURRENT_RATIO() > 1.5)
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(PB() < 1) & (ROE() > 0.05) & (CURRENT_RATIO() > 1.5)",
    check_date="2026-01-23"
)
```

---

### Scenario 10: Technical + Fundamental Combined

**User requirement**: Screen for stocks with PE < 20, ROE > 10%, along with MA golden cross and RSI oversold

### Condition Breakdown

1. **Low valuation**: `PE() < 20`
2. **Good quality**: `ROE() > 0.1`
3. **MA golden cross**: `CROSS(MA(CLOSE, 5), MA(CLOSE, 20))`
4. **RSI oversold**: `RSI(14) < 40`

### Multi-step Screening Strategy

**Step 1: Fundamental screening**
```python
factors_screen(
    market="cn",
    formula="(PE() < 20) & (ROE() > 0.1)",
    check_date="2026-01-23"
)
```

**Step 2: Technical screening**
```python
factors_screen(
    market="cn",
    formula="CROSS(MA(CLOSE, 5), MA(CLOSE, 20)) & (RSI(14) < 40)",
    check_date="2026-01-23",
    symbols=step1_results
)
```

### Or Use a Single Formula

```python
factors_screen(
    market="cn",
    formula="(PE() < 20) & (ROE() > 0.1) & (CROSS(MA(CLOSE, 5), MA(CLOSE, 20))) & (RSI(14) < 40)",
    check_date="2026-01-23"
)
```

---

### Scenario 11: High-Dividend Quality Stocks

**User requirement**: Screen for quality stocks with EPS > 1, PE < 15, and price above the 20-day moving average

### Formula Construction

```python
(EPS() > 1) & (PE() < 15) & (CLOSE > MA(CLOSE, 20))
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(EPS() > 1) & (PE() < 15) & (CLOSE > MA(CLOSE, 20))",
    check_date="2026-01-23"
)
```

---

### Scenario 12: Low-Leverage Financially Sound Stocks

**User requirement**: Screen for financially sound companies with debt-to-asset ratio < 40%, current ratio > 2, and gross profit margin > 25%

### Formula Construction

```python
(DEBT_RATIO() < 0.4) & (CURRENT_RATIO() > 2) & (GPM() > 0.25)
```

### MCP Call

```python
factors_screen(
    market="cn",
    formula="(DEBT_RATIO() < 0.4) & (CURRENT_RATIO() > 2) & (GPM() > 0.25)",
    check_date="2026-01-23"
)
```

---

## More Fundamental Strategy Templates

### Valuation Strategies

```python
# Low valuation
PE() < 15

# Deep value (below book value)
PB() < 1

# PEG strategy (undervalued growth)
(PEG() < 1) & (PEG() > 0)

# Multi-factor value
(PE_TTM() < 15) & (PB() < 2) & (PS() < 2)
```

### Quality Strategies

```python
# High ROE blue-chip stocks
ROE() > 0.2

# High gross margin + high net margin
(GPM() > 0.4) & (NPM() > 0.15)

# Strong cash flow
(OCFPS() > EPS()) & (EPS() > 0)

# Asset-light high efficiency
(ROA() > 0.1) & (TOTAL_ASSET_TURNOVER() > 0.8)
```

### Growth Strategies

```python
# High growth
(REVENUE_GROWTH_RATE() > 0.3) & (NET_PROFIT_GROWTH_RATE() > 0.3)

# Accelerating growth
(NET_PROFIT_GROWTH_RATE() > REVENUE_GROWTH_RATE()) & (REVENUE_GROWTH_RATE() > 0.2)

# Earnings growth
(EPS_GROWTH_RATE() > 0.25) & (EPS() > 0)
```

### Margin of Safety Strategies

```python
# Financially sound
(DEBT_RATIO() < 0.5) & (CURRENT_RATIO() > 2) & (QUICK_RATIO() > 1)

# Low leverage high profitability
(DEBT_TO_EQUITY_RATIO() < 0.5) & (ROE() > 0.15)

# Cash rich
(CURRENT_RATIO() > 2) & (OCFPS() > 0)
```

### Technical + Fundamental Combined

```python
# Value + oversold
(PE() < 15) & (RSI(14) < 30)

# Quality + trend
(ROE() > 0.15) & (CLOSE > MA(CLOSE, 60))

# Growth + breakout
(NET_PROFIT_GROWTH_RATE() > 0.3) & (CLOSE > HHV(HIGH, 20))

# Undervalued + golden cross
(PB() < 1.5) & (CROSS(MA(CLOSE, 5), MA(CLOSE, 20)))

# Composite factors
(PE() < 20) & (ROE() > 0.12) & (DEBT_RATIO() < 0.5) & (CLOSE > MA(CLOSE, 20))
```
