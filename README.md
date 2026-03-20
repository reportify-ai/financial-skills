# Reportify Financial Skills

A collection of financial analysis skills for AI agents, powered by [Reportify](https://reportify.cn).

## Skills

| Skill | Description |
|-------|-------------|
| **reportify-ai** | Comprehensive CLI reference for all Reportify modules (search, stock, quant, agent) |
| **stock-analysis** | Individual stock analysis + deep financial research |
| **stock-screener** | Quantitative stock screening + strategy backtesting |

Each skill is self-contained and can be installed independently.

## Installation

### CLI (required for all skills)

```bash
pip3 install reportify-cli
```

### Python SDK (required for stock-screener)

```bash
pip3 install reportify-sdk
```

### Backtesting dependencies (optional, for stock-screener backtrader mode)

```bash
pip3 install backtrader pandas
```

### Configuration

Set your Reportify API key:
```bash
export REPORTIFY_API_KEY="your_api_key"
```

## Quick Start

```bash
reportify-cli search webpages --input '{"query": "NVIDIA earnings"}' --format json
reportify-cli stock quote --input '{"symbol": "NVDA"}' --format table
reportify-cli quant screen --input '{"formula": "(PE_TTM() < 20) & (ROE() > 0.15)"}' --format json
```

Shortcuts (if configured): `$search` = `reportify-cli search`, `$stock` = `reportify-cli stock`, `$quant` = `reportify-cli quant`, `$agent` = `reportify-cli agent`
