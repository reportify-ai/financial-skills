# Reportify Financial Skills

A collection of financial analysis skills for AI agents, powered by [Reportify](https://reportify.cn).

## Skills

| Skill | Description |
|-------|-------------|
| **reportify-ai** | Comprehensive CLI reference for all Reportify modules (search, stock, quant, agent) |
| **stock-analysis** | Individual stock analysis + deep financial research |
| **stock-screener** | Quantitative stock screening + strategy backtesting |

Each skill is self-contained and can be installed independently.

## Step 1: Install CLI & SDK

```bash
pip3 install reportify-cli                # required for all skills
pip3 install reportify-sdk                # required for stock-screener
pip3 install backtrader pandas            # optional, for backtesting
```

## Step 2: Install Skills

### Universal

```bash
npx skills add reportify-ai/financial-skills --all -y -g
```

### OpenClaw

Send the following message in any OpenClaw conversation:

```
Install reportify skills: https://raw.githubusercontent.com/reportify-ai/financial-skills/main/install.md
```

### Claude Code

```bash
claude plugin marketplace add reportify-ai/financial-skills
claude plugin install reportify-financial-skills
```

Or from within a session:

```
/plugin marketplace add reportify-ai/financial-skills
/plugin install reportify-financial-skills
```

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
cp -r /tmp/reportify-skills/skills/reportify-ai ~/.agents/skills/
cp -r /tmp/reportify-skills/skills/stock-analysis ~/.agents/skills/
cp -r /tmp/reportify-skills/skills/stock-screener ~/.agents/skills/
```

## Step 3: Set API Key

Get your key at https://reportify.cn/skills/api-keys

```bash
export REPORTIFY_API_KEY="your_api_key"
```

To persist, add to your shell config:

```bash
# macOS
echo 'export REPORTIFY_API_KEY="your_api_key"' >> ~/.zshrc

# Linux
echo 'export REPORTIFY_API_KEY="your_api_key"' >> ~/.bashrc

# Windows (PowerShell)
[Environment]::SetEnvironmentVariable("REPORTIFY_API_KEY", "your_api_key", "User")
```

## Quick Start

```bash
reportify-cli search webpages --input '{"query": "NVIDIA earnings"}' --format json
reportify-cli stock quote --input '{"symbol": "NVDA"}' --format table
reportify-cli quant screen --input '{"formula": "(PE_TTM() < 20) & (ROE() > 0.15)"}' --format json
```

Shortcuts (if configured): `$search` = `reportify-cli search`, `$stock` = `reportify-cli stock`, `$quant` = `reportify-cli quant`, `$agent` = `reportify-cli agent`
