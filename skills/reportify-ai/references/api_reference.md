# Reportify API Reference

## Common Parameters

### Symbol Format
Stock symbols must use `market:ticker` format:
- **Shanghai Exchange**: `SH:ticker` (e.g., `SH:600519` for Kweichow Moutai)
- **Shenzhen Exchange**: `SZ:ticker` (e.g., `SZ:000001` for Ping An Bank)
- **Hong Kong**: `HK:ticker` (e.g., `HK:00700` for Tencent)
- **US Market**: `US:ticker` (e.g., `US:AAPL` for Apple, `US:TSLA` for Tesla)

### Date/Time Format
- **Date**: `YYYY-MM-DD` (e.g., `2024-01-15`)
- **DateTime**: `YYYY-MM-DD HH:MM:SS` (e.g., `2024-01-15 09:30:00`)

### Output Formats
- `json` - JSON (default)
- `table` - Formatted table
- `csv` - CSV format
- `markdown` / `md` - Markdown table

---

## Search Module Parameters

### search webpages
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | ✓ | - | Search keywords |
| `num` | int | | 10 | Results count (max 100) |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search all
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | ✓ | - | Search keywords |
| `num` | int | | 10 | Results count (max 100) |
| `categories` | list[str] | | - | Filter: news, reports, filings, transcripts, socials |
| `symbols` | list[str] | | - | Stock symbols in market:ticker format (e.g., US:AAPL, HK:00700) |
| `industries` | list[str] | | - | Industry filter |
| `channel_ids` | list[str] | | - | Channel ID filter |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search news
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | ✓ | - | Search keywords |
| `num` | int | | 10 | Results count |
| `symbols` | list[str] | | - | Stock symbols in market:ticker format |
| `channel_ids` | list[str] | | - | Channel ID filter |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search reports
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | ✓ | - | Search keywords |
| `num` | int | | 10 | Results count |
| `symbols` | list[str] | | - | Stock symbols in market:ticker format |
| `industries` | list[str] | | - | Industry filter |
| `channel_ids` | list[str] | | - | Channel ID filter |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search filings
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | ✓ | - | Search keywords |
| `symbols` | list[str] | ✓ | - | Stock symbols in market:ticker format (required) |
| `num` | int | | 10 | Results count |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search conference_calls / earnings_pack
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | | - | Search keywords (optional; if omitted, returns by date desc; if provided, sorts by relevance) |
| `symbols` | list[str] | ✓ | - | Stock symbols in market:ticker format (required) |
| `num` | int | | 10 | Results count |
| `fiscal_year` | str | | - | Fiscal year (e.g., "2025") |
| `fiscal_quarter` | str | | - | Fiscal quarter (Q1-Q4) |
| `sort_by` | str | | date_desc | Sort: date_desc, relevance, date_asc |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search socials
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | ✓ | - | Search keywords |
| `num` | int | | 10 | Results count |
| `symbols` | list[str] | | - | Stock symbols in market:ticker format |
| `channel_ids` | list[str] | | - | Channel ID filter |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search minutes
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | ✓ | - | Search keywords |
| `num` | int | | 10 | Results count |
| `symbols` | list[str] | | - | Stock symbols in market:ticker format |
| `start_datetime` | str | | - | Start time |
| `end_datetime` | str | | - | End time |

### search crawl
| Parameter | Type | Required | Default | Description                                                                 |
|-----------|------|----------|---------|-----------------------------------------------------------------------------|
| `urls` | list[str] | ✓ | - | List of URLs to crawl                                                       |
| `text` | bool/dict | | true | Extract full text content(set true), or pass dict for advanced options |
| `text.max_characters` | int | | - | Maximum characters to return per page                                       |
| `text.include_html_tags` | bool | | false | Whether to preserve HTML tags                                               |
| `text.verbosity` | string | | "full" | `compact` (removes boilerplate) or `full` (complete content)                |
| `highlights` | dict | | - | Extract query-relevant highlights                                           |
| `highlights.query` | string | | - | Query for extracting relevant highlights                                    |
| `highlights.max_characters` | int | | - | Max characters per highlight                                                |
| `highlights.num_sentences` | int | | 5 | Number of sentences per highlight                                           |
| `highlights.highlights_per_url` | int | | 1 | Number of highlights per URL                                                |
| `summary` | dict | | - | LLM-generated summary                                                       |
| `summary.query` | string | | - | Query instruction for summary                                               |
| `subpages` | int | | - | Max subpages to crawl per URL                                               |
| `subpage_target` | list[str] | | - | Keywords to prioritize when selecting subpages                              |
| `max_age_hours` | int | | - | Max age of cached content in hours                                          |
| `livecrawl_timeout` | int | | - | Timeout in ms for live crawling fallback                                    |

---

## Stock Module Parameters

### stock overview
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbols` | string | | - | Stock symbols, comma-separated (e.g., "AAPL,00700,600519"). Also accepts `symbol` for single stock |
| `names` | string | | - | Stock names, comma-separated (e.g., "Tencent,Apple") |

### stock quote
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | string | ✓ | - | Stock symbol (e.g., "AAPL", "688001", "00700") |
| `start_date` | str | | - | Start date (YYYY-MM-DD) |
| `end_date` | str | | - | End date (YYYY-MM-DD) |

### stock index_quote
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | string | ✓ | - | Index code (e.g., "000300", "HSI", "SPX") |
| `start_date` | str | | - | Start date (YYYY-MM-DD) |
| `end_date` | str | | - | End date (YYYY-MM-DD) |

### stock shareholders
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | string | ✓ | - | Stock symbol |
| `type` | string | | shareholders | "shareholders" or "outstanding_shareholders" |
| `limit` | int | | 10 | Number of shareholders to return |

### stock revenue_breakdown
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | string | ✓ | - | Stock symbol |
| `period` | str | | - | Report period (e.g., "FY", "Q2") |
| `limit` | int | | 6 | Number of records |
| `start_date` | str | | - | Start date |
| `end_date` | str | | - | End date |
| `fiscal_year` | str | | - | Fiscal year |

### stock income_statement / balance_sheet / cashflow_statement
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | string | ✓ | - | Stock symbol |
| `period` | string | | annual | annual / quarterly |
| `limit` | int | | 8 | Number of periods |
| `start_date` | str | | - | Start date |
| `end_date` | str | | - | End date |
| `calendar` | str | | fiscal | calendar / fiscal |
| `fiscal_year` | str | | - | Specific fiscal year |
| `fiscal_quarter` | str | | - | Q1, Q2, Q3, Q4, FY, H1 |

### stock index_constituents
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | string | ✓ | - | Index code (e.g., "000300" for CSI 300, "399006" for ChiNext) |

### stock index_tracking_funds
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | string | ✓ | - | Index code (e.g., "000300", "000905") |

### stock industry_constituents
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `market` | string | ✓ | - | Stock market: cn, hk, us |
| `name` | string | ✓ | - | Industry name (e.g., "Defense", "Technology") |
| `type` | string | | - | Classification: sw (Shenwan), wind, hs (Hang Seng), GICS |

### stock earnings_calendar
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `market` | string | ✓ | - | Stock market: cn, hk, us |
| `start_date` | str | | - | Start date |
| `end_date` | str | | - | End date |
| `symbol` | str | | - | Stock symbol |

### stock ipo_calendar_hk
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `status` | string | | Filing | IPO status: Filing, Hearing, Priced |

---

## Quant Module Parameters

### quant ohlcv
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | str | ✓ | - | Stock code (e.g., 600519) |
| `market` | str | | cn | cn / hk / us |
| `start_date` | str | | 1 month ago | Start date |
| `end_date` | str | | today | End date |

### quant ohlcv_batch
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbols` | list[str] | ✓ | - | Stock codes (e.g., [600519, 000001]) |
| `market` | str | | cn | cn / hk / us |
| `start_date` | str | | 1 month ago | Start date |
| `end_date` | str | | today | End date |

### quant kline
Unified kline endpoint supporting all periods via `kline_type`.
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | str | ✓ | - | Stock code (e.g., 600519) |
| `kline_type` | str | | 1D | `1M`/`5M`/`15M`/`30M`/`60M` (intraday), `1D`/`1W`/`1MO` |
| `market` | str | | cn | cn / hk / us |
| `stock_type` | str | | stock | stock / etf / index / sw |
| `start_datetime` | str | | - | Start datetime (YYYY-MM-DD HH:MM:SS) |
| `end_datetime` | str | | - | End datetime (YYYY-MM-DD HH:MM:SS) |

### quant kline_batch
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbols` | list[str] | ✓ | - | Stock codes (e.g., [600519, 000001]) |
| `kline_type` | str | | 1D | Same as kline |
| `market` | str | | cn | cn / hk / us |
| `stock_type` | str | | stock | stock / etf / index / sw |
| `start_datetime` | str | | - | Start datetime (YYYY-MM-DD HH:MM:SS) |
| `end_datetime` | str | | - | End datetime (YYYY-MM-DD HH:MM:SS) |

### quant minute
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbol` | str | ✓ | - | Stock code (e.g., 600519) |
| `start_datetime` | str | ✓ | - | Start datetime (YYYY-MM-DD HH:MM:SS) |
| `end_datetime` | str | ✓ | - | End datetime (YYYY-MM-DD HH:MM:SS) |
| `market` | str | | cn | cn / hk / us |

### quant minute_batch
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbols` | list[str] | ✓ | - | Stock codes (e.g., [600519, 000001]) |
| `start_datetime` | str | ✓ | - | Start datetime (YYYY-MM-DD HH:MM:SS) |
| `end_datetime` | str | ✓ | - | End datetime (YYYY-MM-DD HH:MM:SS) |
| `market` | str | | cn | cn / hk / us |

### quant factors_compute
Compute factors (includes technical indicators like MACD/RSI/KDJ and fundamental factors like PE/ROE).
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `symbols` | list[str] | ✓ | - | Stock codes (e.g., 600519 for CN market) |
| `formula` | str | ✓ | - | Formula expression. Both sides of &/\|/AND/OR must be wrapped in () |
| `market` | str | | cn | cn / hk / us |
| `start_date` | str | | 3 months ago | Start date |
| `end_date` | str | | today | End date |

### quant factors
List available factors, functions, and indicators.
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `market` | str | | cn | cn / hk / us |

### quant backtest
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `start_date` | str | ✓ | - | Backtest start (YYYY-MM-DD) |
| `end_date` | str | ✓ | - | Backtest end (YYYY-MM-DD) |
| `symbols` | list[str] | | None | Stock codes (REQUIRED if filter_formula is not provided) |
| `filter_formula` | str | | None | Pre-filter formula (REQUIRED if symbols is not provided) |
| `entry_formula` | str | | None | Entry signal formula (mutually exclusive with strategy_code) |
| `strategy_code` | str | | None | Custom strategy python code (mutually exclusive with entry_formula) |
| `exit_formula` | str | | None | Exit signal formula |
| `market` | str | | cn | Market |
| `initial_cash` | float | | 100000.0 | Initial capital |
| `commission` | float | | 0.0 | Commission rate for both buy and sell |
| `buy_commission` | float | | 0.0 | Buy commission (takes priority over commission) |
| `sell_commission` | float | | 0.0 | Sell commission (takes priority over commission) |
| `min_commission_amount` | float | | 0.0 | Minimum commission per trade in CNY |
| `slippage` | float | | 0.0 | Slippage ratio |
| `stop_loss` | float | | 0.0 | Stop loss ratio (0=disabled) |
| `position_size` | float \| None | | None | Max position size ratio per stock (None=no limit) |
| `max_positions` | int \| None | | None | Max number of holding stocks (None=no limit) |
| `min_volume` | int | | 100 | Minimum trading volume |
| `auto_close` | bool | | true | Auto close positions |
| `cheat_on_open` | bool | | false | Execute T-day signals at T-day open (false=T+1 open) |
| `signal_factors` | dict[str, str] | | None | Precomputed factors for strategy_code |

### quant factors_screen
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `formula` | str | ✓ | - | Screening formula. Both sides of &/\|/AND/OR must be wrapped in () |
| `market` | str | | cn | Market |
| `check_date` | str | | latest | Check date |
| `symbols` | list[str] | | - | Stock codes pool (None=all market) |

---

## Agent Module Parameters

### agent create_conversation
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `agent_id` | int | | - | Agent ID (optional, uses system default if not provided) |
| `title` | string | | - | Conversation title |
| `conversation_type` | string | | - | Conversation type: agent_chat, task_chat, debug_chat, bot_chat |
| `meta` | dict | | - | Metadata dict |
| `channel` | string | | - | Source channel for callback (universal-bridge, openclaw-weixin, telegram) |

### agent chat
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `conversation_id` | int | ✓ | - | Conversation ID |
| `message` | string | ✓ | - | User message content |
| `documents` | list[dict] | | - | Related documents, each with doc_id, doc_title, file_type |
| `stream` | bool | | true | Whether to use streaming response |
| `background` | bool | | false | Run agent task in background, returns immediately when true |

### agent get_conversation
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `conversation_id` | int | ✓ | - | Conversation ID |

### agent list_messages
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `conversation_id` | int | ✓ | - | Conversation ID |
| `limit` | int | | 10 | Number of messages (max 100) |
| `before_message_id` | int | | - | Fetch messages before this ID |

### agent get_message_events
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `conversation_id` | int | ✓ | - | Conversation ID |
| `assistant_message_id` | string | ✓ | - | Assistant message ID |
| `stream` | bool | | true | Stream events via SSE |
| `timeout` | int | | 1800 | Timeout in seconds |
| `from_offset` | int | | 0 | Start from specific offset |

### agent cancel_execution
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `conversation_id` | int | ✓ | - | Conversation ID |
| `assistant_message_id` | string | ✓ | - | Assistant message ID to cancel |

### agent get_file
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `file_id` | string | ✓ | - | Agent generated file ID |

