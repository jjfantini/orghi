# AGENTS.md

## Cursor Cloud specific instructions

### Project overview

Nanobot (orghi fork) is an ultra-lightweight personal AI assistant framework. See `README.md` for full docs.

### Development environment

- **Python >=3.11**, **Node.js >=20** (for WhatsApp bridge in `bridge/`)
- Uses `uv` for Python package management with a virtualenv at `.venv`
- Activate with: `source .venv/bin/activate`
- Install deps: `uv pip install -e ".[dev,matrix]"` (from repo root)
- Bridge deps: `cd bridge && npm install && npm run build`

### Key commands

| Task | Command |
|------|---------|
| Lint | `ruff check .` |
| Tests | `python -m pytest tests/ -v` |
| Onboard | `nanobot onboard` |
| Status | `nanobot status` |
| Agent CLI | `nanobot agent -m "Hello!"` |
| Gateway | `nanobot gateway` |

### Non-obvious caveats

- **LLM API key required**: The agent (`nanobot agent`) needs at least one LLM provider API key in `~/.nanobot/config.json`. The `OPENROUTER_API_KEY` env secret is available. Before running the agent, initialize config and inject the key:
  ```
  nanobot onboard  # idempotent, creates config if missing
  python -c "import json,os; c=json.load(open(os.path.expanduser('~/.nanobot/config.json'))); c.setdefault('providers',{}).setdefault('openrouter',{})['apiKey']=os.environ['OPENROUTER_API_KEY']; json.dump(c,open(os.path.expanduser('~/.nanobot/config.json'),'w'),indent=2)"
  ```
- **Telegram send_only tests hang**: `tests/orghi/test_telegram_send_only.py` hangs indefinitely in CI/cloud environments (async polling issue). Skip with `--ignore=tests/orghi/test_telegram_send_only.py`.
- **Matrix tests**: 5 pre-existing failures in `tests/test_matrix_channel.py` due to mock signature mismatches. These are upstream issues.
- **Ruff lint**: 36 pre-existing lint warnings. These are in the existing codebase.
- **Config location**: `~/.nanobot/config.json` - created by `nanobot onboard`. Workspace files at `~/.nanobot/workspace/`.
- **Virtual environment**: The `.venv` directory is at the repo root. Always activate it before running nanobot commands.
