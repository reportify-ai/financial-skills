---
name: stock-analysis
description: Analyze stocks and companies using reportify-cli. Get company profiles, financial statements, stock quotes, technical indicators, analyst reports, earnings transcripts, and regulatory filings. Also handles deep financial research — cross-quarter earnings analysis, FOMC analysis, multi-source synthesis, and news aggregation. Use when user asks about a specific stock, company, investment analysis, earnings review, or requests deep financial research.
---

# Stock Analysis & Financial Research

Analyze individual stocks and conduct financial research using `reportify-cli` commands. For complex multi-step research, delegate to Reportify Agent.

**Shortcuts** (if configured): `$search` = `reportify-cli search`, `$stock` = `reportify-cli stock`, `$quant` = `reportify-cli quant`, `$agent` = `reportify-cli agent`

**Install**: `pip3 install reportify-cli`

**API Key**: Get your key at https://reportify.cn/skills then set:
```bash
export REPORTIFY_API_KEY="your_api_key"
```

## Symbol Format

| Module | Format | Examples |
|--------|--------|----------|
| `stock` / `quant` | Plain code | `AAPL`, `600519`, `00700` |
| `search` symbols | `Market:Code` | `US:AAPL`, `SH:600519`, `HK:00700` |

---

## CLI Tools

### Company Data (`reportify-cli stock`)
- `overview` — Company profile, business summary, executives
- `income_statement` — Revenue, profit trends (annual/quarterly)
- `balance_sheet` — Assets, liabilities structure
- `cashflow_statement` — Cash generation, capex
- `revenue_breakdown` — Revenue by segment/region
- `shareholders` — Major shareholders

### Market Data
- `reportify-cli stock quote` — Daily price, volume, market cap, PE, PS
- `reportify-cli stock index_quote` — Index price history
- `reportify-cli quant minute` — Real-time intraday price (use `start_datetime`/`end_datetime` with `HH:MM:SS`)
- `reportify-cli quant ohlcv` — Daily OHLCV candles

### Technical Analysis (`reportify-cli quant`)
- `compute_indicators` — RSI, MACD, KDJ, BOLL, ATR, etc.
- `compute_factors` — PE, PB, ROE, ROA, etc.

### Document Research (`reportify-cli search`)
- `reports` — Research reports (requires `query`)
- `news` — Financial news (requires `query`)
- `conference_calls` — Earnings call transcripts (`symbols` required, `query` optional)
- `earnings_pack` — Earnings report packs (`symbols` required, `query` optional)
- `filings` — Company filings (requires `query` + `symbols`)
- `webpages` — Web search (preferred over built-in web_search)

---

## Workflows

### 1. Company Overview
```
"Tell me about AAPL"
→ reportify-cli stock overview --input '{"symbols": "AAPL"}'
→ reportify-cli stock quote --input '{"symbol": "AAPL"}'
→ reportify-cli search earnings_pack --input '{"symbols": ["US:AAPL"], "num": 1}'
```

### 2. Investment Analysis
```
"Is TSLA a good buy?"
→ reportify-cli stock overview + stock quote (fundamentals + valuation)
→ reportify-cli stock income_statement (revenue & profit trends)
→ reportify-cli quant compute_indicators --input '{"symbols": ["TSLA"], "formula": "RSI(14)"}'
→ reportify-cli search reports --input '{"query": "Tesla", "symbols": ["US:TSLA"]}'
→ reportify-cli search conference_calls --input '{"symbols": ["US:TSLA"], "num": 1}'
```

### 3. Financial Deep Dive
```
"Analyze MSFT's financials"
→ reportify-cli stock income_statement --input '{"symbol": "MSFT", "period": "annual", "limit": 4}'
→ reportify-cli stock balance_sheet --input '{"symbol": "MSFT", "period": "annual"}'
→ reportify-cli stock cashflow_statement --input '{"symbol": "MSFT"}'
→ reportify-cli stock revenue_breakdown --input '{"symbol": "MSFT", "limit": 4}'
→ reportify-cli quant compute_factors --input '{"symbols": ["MSFT"], "formula": "PE_TTM()"}'
```

### 4. Earnings Review
```
"AAPL earnings review"
→ reportify-cli search earnings_pack --input '{"symbols": ["US:AAPL"], "num": 1}'
→ reportify-cli search conference_calls --input '{"symbols": ["US:AAPL"], "num": 1}'
→ reportify-cli stock income_statement --input '{"symbol": "AAPL", "period": "quarterly", "limit": 4}'
→ reportify-cli search news --input '{"query": "Apple earnings", "symbols": ["US:AAPL"]}'
```

### 5. Multi-Stock Comparison
```
"Compare AAPL vs MSFT vs GOOGL"
→ reportify-cli stock overview for each
→ reportify-cli stock quote for each (side-by-side valuation)
→ reportify-cli quant compute_factors --input '{"symbols": ["AAPL","MSFT","GOOGL"], "formula": "PE_TTM()"}'
→ Present comparison table
```

### 6. News & Sentiment
```
"NVIDIA latest news"
→ reportify-cli search news --input '{"query": "NVIDIA", "symbols": ["US:NVDA"], "num": 10}'
→ reportify-cli search socials --input '{"query": "NVIDIA AI", "num": 5}'
→ reportify-cli stock quote --input '{"symbol": "NVDA"}' (market reaction context)
```

---

## Deep Research Mode (via Agent)

For tasks too complex for single CLI commands, delegate to Reportify Agent:

**When to delegate:**
- Cross-quarter comparison: "How did AWS capex guidance change across Q1-Q4 2025?"
- Multi-source synthesis: "What did Powell say about monetary policy at Jan 29 FOMC?"
- Management commentary extraction across quarters
- Research requiring document retrieval + analysis + structured output

**Workflow:**
```bash
# Step 1: Create conversation
RESULT=$(reportify-cli agent create_conversation --input "{\"conversation_type\": \"bot_chat\", \"title\": \"用户的问题\"}")
CONV_ID=$(echo "$RESULT" | python3 -c "import sys,json; print(json.load(sys.stdin)['id'])")
TASK_URL=$(echo "$RESULT" | python3 -c "import sys,json; print(json.load(sys.stdin).get('url',''))")

# Step 2: Send task — CRITICAL: pass user's original question verbatim
CHAT_RESULT=$(reportify-cli agent chat --input "{\"conversation_id\": ${CONV_ID}, \"message\": \"用户的原始问题\", \"background\": true}")

# Step 3: Notify user with TASK_URL
```

**Deep research examples:**
- "2025Q3财报电话会里，台积电对CoWos的相关讨论是什么"
- "在2025Q1-Q4电话会中，亚马逊管理层对 AWS 资本开支的态度分别是什么？"
- "鲍威尔在2026年1月29日FOMC新闻发布会中对未来货币政策的描述"
- "在2025Q4电话会中，Meta管理层对AI推理算力需求的表述"

---

## Best Practices

1. **Start with overview** — `reportify-cli stock overview` + `stock quote` for context
2. **Combine sources** — Financial statements + indicators + documents for complete picture
3. **Correct symbol format** — `stock`/`quant`: plain code; `search`: `Market:Code`
4. **Cross-reference** — Compare earnings call claims with actual financials
5. **Delegate complexity** — Single CLI command can handle it → use CLI; multi-step → use Agent

## Reference

For detailed API parameters, see [references/api_reference.md](references/api_reference.md).
