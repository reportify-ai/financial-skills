# Reportify Financial Skills

A collection of financial analysis skills for AI agents, powered by [Reportify](https://reportify.cn).

## Skills

| Skill | Description |
|-------|-------------|
| **reportify-ai** | Comprehensive CLI reference for all Reportify modules (search, stock, quant, agent) |
| **stock-analysis** | Individual stock analysis + deep financial research |
| **stock-screener** | Quantitative stock screening + strategy backtesting |

Each skill is self-contained and can be installed independently.

## Install via Claude Code

Add the marketplace, then install the skills you need:

```bash
claude plugin add-marketplace https://github.com/reportify-ai/financial-skills

# Install all three
claude plugin install reportify-ai
claude plugin install stock-analysis
claude plugin install stock-screener

# Or just the ones you need
claude plugin install reportify-ai          # CLI reference (recommended)
claude plugin install stock-screener        # if you need quant screening
```

Or from within a Claude Code session:

```
/plugin add-marketplace https://github.com/reportify-ai/financial-skills
/plugin install reportify-ai
/plugin install stock-analysis
/plugin install stock-screener
```

## Install via OpenClaw

Send the following message in any OpenClaw conversation to install Reportify financial skills (reportify-ai, stock-analysis, stock-screener):

```
Install reportify skills: https://raw.githubusercontent.com/reportify-ai/financial-skills/main/install.md
```

## Manual Installation

### 1. Install CLI (required for all skills)

```bash
pip3 install reportify-cli
```

### 2. Install Python SDK (required for stock-screener)

```bash
pip3 install reportify-sdk
```

### 3. Install backtesting dependencies (optional)

```bash
pip3 install backtrader pandas
```

### 4. Set your API key

Get your key at https://reportify.cn/skills/api-keys

```bash
export REPORTIFY_API_KEY="your_api_key"
```

### 5. Download skills

See [install.md](install.md) for the full download commands.

## Quick Start

```bash
reportify-cli search webpages --input '{"query": "NVIDIA earnings"}' --format json
reportify-cli stock quote --input '{"symbol": "NVDA"}' --format table
reportify-cli quant screen --input '{"formula": "(PE_TTM() < 20) & (ROE() > 0.15)"}' --format json
```

Shortcuts (if configured): `$search` = `reportify-cli search`, `$stock` = `reportify-cli stock`, `$quant` = `reportify-cli quant`, `$agent` = `reportify-cli agent`
