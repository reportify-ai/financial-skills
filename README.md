# Reportify Financial Skills

A collection of financial analysis skills for AI agents, powered by [Reportify](https://reportify.cn).

## Skills

| Skill | Description |
|-------|-------------|
| **reportify-ai** | Comprehensive CLI reference for all Reportify modules (search, stock, quant, agent) |
| **stock-analysis** | Individual stock analysis + deep financial research |
| **stock-screener** | Quantitative stock screening + strategy backtesting |

Each skill is self-contained and can be installed independently.

## Install via OpenClaw

Send the following message in any OpenClaw conversation to install Reportify financial skills (reportify-ai, stock-analysis, stock-screener):

```
Install reportify skills: https://raw.githubusercontent.com/reportify-ai/financial-skills/main/install.md
```

## Install in Codex / Claude Code

### Claude Code

```bash
claude plugin add-marketplace https://github.com/reportify-ai/financial-skills
claude plugin install reportify-ai
claude plugin install stock-analysis
claude plugin install stock-screener
```

Or from within a session: `/plugin add-marketplace ...` then `/plugin install ...`

### Codex

Add to your marketplace config (`~/.agents/plugins/marketplace.json`):

```json
{
  "name": "reportify",
  "plugins": [{
    "name": "reportify-financial-skills",
    "source": {
      "source": "git",
      "url": "https://github.com/reportify-ai/financial-skills"
    },
    "category": "Finance"
  }]
}
```

Or copy skills manually:

```bash
git clone https://github.com/reportify-ai/financial-skills.git /tmp/reportify-skills
cp -r /tmp/reportify-skills/reportify-ai ~/.agents/skills/
cp -r /tmp/reportify-skills/stock-analysis ~/.agents/skills/
cp -r /tmp/reportify-skills/stock-screener ~/.agents/skills/
```

### Prerequisites

```bash
pip3 install reportify-cli
export REPORTIFY_API_KEY="your_key"  # Get at https://reportify.cn/skills/api-keys
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
