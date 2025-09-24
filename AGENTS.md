# Repository Guidelines

## Project Structure & Module Organization
- `mcp_server_snowflake/`: Python package with server and tools
  - `server.py`: entrypoint (`mcp-server-snowflake` / `snowflake-labs-mcp`)
  - `utils.py`, `server_utils.py`: helpers and middleware
  - `cortex_services/`, `object_manager/`, `query_manager/`, `semantic_manager/`: tool modules
  - `tests/`: pytest suite (`test_*.py`)
- `services/`: example configs (`configuration.yaml`, `tools_config.yaml`)
- `docker/`, `docker-compose.yml`, `fly.toml`: container and deployment assets
- Tooling: `pyproject.toml` (uv/packaging), `ruff.toml`, `.pre-commit-config.yaml`

## Build, Test, and Development Commands
- Create env and install deps: `uv sync` (Python ≥ 3.11)
- Run server locally (stdio):
  - `uvx snowflake-labs-mcp --service-config-file services/tools_config.yaml --connection-name default`
- Run server (HTTP for containers):
  - `uvx snowflake-labs-mcp --service-config-file services/tools_config.yaml --transport streamable-http --endpoint /snowflake-mcp`
- Tests: `pytest -q`
- Lint: `ruff check .`
- Format: `ruff format .`
- Pre-commit: `pre-commit install && pre-commit run -a`

## Coding Style & Naming Conventions
- Python 3.11; 4‑space indent, 88 char line length, double quotes (see `ruff.toml`)
- Module/file names: snake_case; classes: `PascalCase`; functions/vars: `snake_case`
- Type hints required for public functions; prefer `pydantic` models for I/O schemas

## Testing Guidelines
- Framework: `pytest`; place tests in `mcp_server_snowflake/tests/` as `test_*.py`
- Keep tests fast and isolated; mock Snowflake connections (see `test_service_config.py`)
- Run selectively with `pytest -k <expr>`; add new tests for added behavior

## Commit & Pull Request Guidelines
- Conventional Commits used in history: e.g., `feat: ...`, `fix: ...`, `docs: ...`, `chore: ...`, `ci: ...`
- PRs must include: concise description, rationale, screenshots/logs when UI/CLI behavior changes, and linked issues
- Ensure `ruff`, `pytest`, and pre-commit hooks pass before requesting review

## Security & Configuration Tips
- Never commit secrets. Use env vars: `SNOWFLAKE_ACCOUNT`, `SNOWFLAKE_USER`, `SNOWFLAKE_PASSWORD`
- Prefer least-privilege SQL via `sql_statement_permissions` in `services/*.yaml` (writes/DDL disabled by default in `tools_config.yaml`)
- Validate configs locally before deploy; for containers, the default CMD reads `/app/services/tools_config.yaml`

