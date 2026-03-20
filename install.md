# Reportify Skill Install Guide

Read this file as the agent that is performing the installation.

Your goal is to install Reportify financial skills for the user.

## Preconditions

- Confirm Python 3.8+ is available.
- Confirm the user has provided a valid `REPORTIFY_API_KEY`.

If the key is missing, direct the user to https://reportify.cn/skills/api-keys to get one, then stop and ask for it before continuing.

## Install Flow

### 1. Remove existing reportify skills

Before installing, remove any existing skills whose directory name starts with `reportify` or `stock-` under `~/.claude/skills`:

```bash
rm -rf ~/.claude/skills/reportify-ai
rm -rf ~/.claude/skills/stock-analysis
rm -rf ~/.claude/skills/stock-screener
```

### 2. Install reportify-cli

```bash
pip3 install --upgrade reportify-cli
```

Verify installation:

```bash
reportify-cli --help
```

If the user needs stock screening with Python SDK or backtesting:

```bash
pip3 install --upgrade reportify-sdk
pip3 install backtrader pandas  # optional, for backtesting
```

### 3. Download skills

Download the skill definitions and references from GitHub:

```bash
# reportify-ai (comprehensive CLI reference)
mkdir -p ~/.claude/skills/reportify-ai/references
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/reportify-ai/SKILL.md -o ~/.claude/skills/reportify-ai/SKILL.md
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/reportify-ai/references/api_reference.md -o ~/.claude/skills/reportify-ai/references/api_reference.md
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/reportify-ai/references/formula.md -o ~/.claude/skills/reportify-ai/references/formula.md

# stock-analysis (individual stock analysis + deep research)
mkdir -p ~/.claude/skills/stock-analysis/references
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/stock-analysis/SKILL.md -o ~/.claude/skills/stock-analysis/SKILL.md
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/stock-analysis/references/api_reference.md -o ~/.claude/skills/stock-analysis/references/api_reference.md

# stock-screener (quantitative screening + backtesting)
mkdir -p ~/.claude/skills/stock-screener/references
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/stock-screener/SKILL.md -o ~/.claude/skills/stock-screener/SKILL.md
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/stock-screener/references/formula.md -o ~/.claude/skills/stock-screener/references/formula.md
curl -fSL https://raw.githubusercontent.com/reportify-ai/financial-skills/main/stock-screener/references/scenarios.md -o ~/.claude/skills/stock-screener/references/scenarios.md
```

### 4. Set the API key for the current session

Use a session-scoped export first. Do not modify `~/.bashrc`, `~/.zshrc`, or other shell startup files unless the user explicitly asks for persistent setup.

```bash
export REPORTIFY_API_KEY="<USER_PROVIDED_KEY>"
```

### 5. Verify the installed files

All of these files should exist after the download:

```bash
echo "=== reportify-ai ==="
test -f ~/.claude/skills/reportify-ai/SKILL.md && echo "SKILL.md OK" || echo "SKILL.md MISSING"
test -f ~/.claude/skills/reportify-ai/references/api_reference.md && echo "api_reference.md OK" || echo "api_reference.md MISSING"
test -f ~/.claude/skills/reportify-ai/references/formula.md && echo "formula.md OK" || echo "formula.md MISSING"

echo "=== stock-analysis ==="
test -f ~/.claude/skills/stock-analysis/SKILL.md && echo "SKILL.md OK" || echo "SKILL.md MISSING"
test -f ~/.claude/skills/stock-analysis/references/api_reference.md && echo "api_reference.md OK" || echo "api_reference.md MISSING"

echo "=== stock-screener ==="
test -f ~/.claude/skills/stock-screener/SKILL.md && echo "SKILL.md OK" || echo "SKILL.md MISSING"
test -f ~/.claude/skills/stock-screener/references/formula.md && echo "formula.md OK" || echo "formula.md MISSING"
test -f ~/.claude/skills/stock-screener/references/scenarios.md && echo "scenarios.md OK" || echo "scenarios.md MISSING"

echo "=== API Key ==="
echo "REPORTIFY_API_KEY=${REPORTIFY_API_KEY:+is set}"
```

### 6. Verify the CLI works

```bash
reportify-cli --help
reportify-cli search webpages --input '{"query": "test", "num": 1}' --format json
```

The help output should list modules: `search`, `stock`, `quant`, `agent`.
The search command should return results without errors.

## Behavior Rules

- Do not say installation succeeded unless the file checks and CLI checks passed.
- If any verification step fails, report the exact failed step and stop.
- If `REPORTIFY_API_KEY` is not set, the CLI will fail — remind the user to set it.

## Optional Persistence

Only if the user explicitly asks for persistent shell configuration:

- Ask which shell file should be updated (`~/.bashrc`, `~/.zshrc`, etc.)
- Explain the exact line that will be added: `export REPORTIFY_API_KEY="..."`
- Update only after confirmation

Default behavior is non-persistent session setup.
