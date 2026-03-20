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

Run the following in Claude Code to auto-install all skills:

```
/install-skill https://raw.githubusercontent.com/reportify-ai/financial-skills/main/install.md
```

Or paste the install URL directly in a conversation:

```
https://raw.githubusercontent.com/reportify-ai/financial-skills/main/install.md
```

Claude Code will read the install guide and set up everything automatically — including downloading skills, installing `reportify-cli`, and configuring your API key.

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
