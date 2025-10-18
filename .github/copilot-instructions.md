<!-- .github/copilot-instructions.md - Guidance for AI coding agents working on PrimerNLP -->

Short goal
--------------
Help maintainers and contributors work productively on this data-science template repository (PrimerNLP). Focus changes on reproducible pipelines in `src/`, tests in `tests/`, and developer workflows driven by `Makefile` and `pyproject.toml`.

Quick context (big picture)
---------------------------
- This repo is a data-science project template. Key folders: `data/` (layered data), `notebooks/`, `src/` (code), `models/`, and `tests/`.
- `src/` is organized into `data/`, `model/`, `inference/` and `pipelines/` (see `src/README.md`). Pipelines follow the Feature -> Training -> Inference separation.
- Environment and dependency management uses `uv` (see `Makefile` and `pyproject.toml`). Tests run with `pytest` and are configured in `pyproject.toml` (pythonpath `src`, tests in `tests`).

What to do first when editing code
----------------------------------
- Run small unit tests locally before larger edits: `make test` (calls `uv run pytest --cov`). Use `make test_verbose` for verbose output.
- To add or update dependencies use `uv add <package>` (see README and Makefile targets `install_env` / `install_data_libs`).
- Run code quality checks with `make check` (runs `pre-commit` via `uv`).

Project-specific conventions and patterns
---------------------------------------
- Single-source `src` import: tests expect `src` on PYTHONPATH (configured via `pyproject.toml`). Import from top-level package paths (e.g., `from src.data...` is acceptable when tests run through pytest/uv).
- Minimal runnable entry points: this template contains `src/tmp_mock.py` and `tests/test_mock.py` as examples. New modules should include small, testable functions and corresponding tests under `tests/` following the same naming pattern (`test_*.py`).
- Pipelines: follow `src/pipelines/<feature|training|inference>_pipeline` structure. Keep side effects (I/O, writing to `data/` or `models/`) inside pipeline entrypoints; pure logic should be in functions that are easy to unit test.
- Configuration: `conf/config.yml` is present but empty; prefer explicit parameters in function signatures and add configuration reads in one place (e.g., `conf/` or a small `src/config.py`) rather than scattering `yaml.safe_load` calls.

Testing and CI notes
--------------------
- Tests are executed with `pytest`. Test configuration lives in `pyproject.toml` (pytest.ini_options). Use `make test` and `make test_coverage` to get XML coverage.
- Keep tests fast and isolated from large data files in `data/`; mock file I/O when reasonable.

Quick examples (what we expect)
------------------------------
- Add dependency: `uv add pandas` or `make install_data_libs` for grouped installs.
- Run tests: `make test_verbose`
- Run linters/formatters: `make check` (pre-commit hooks run via `uv`).
- Adding a pipeline: create `src/pipelines/feature_pipeline/my_pipeline.py` with a small `run()` entrypoint and tests at `tests/pipelines/feature_pipeline/test_my_pipeline.py` that call pure functions with synthetic data.

Integration and external points
--------------------------------
- No external cloud services are referenced by default in this template. If adding integrations (S3, GCS, DBs), centralize credentials access and avoid committing secrets.
- CI workflows live under `.github/workflows/*` (standard templates). Keep changes to workflow files minimal and ensure `uv` is available in CI runners or replace with pip-based commands.

When to ask for human help
--------------------------
- Large refactors that change public APIs across `src/` and `tests/` (ask for a design note).
- Adding new CI dependencies or changing the developer environment (devcontainer, pre-commit hooks).

Files to inspect for more context
--------------------------------
- `Makefile` – developer commands and `uv` usage
- `pyproject.toml` – dependencies, pytest settings, and project metadata
- `src/README.md` – architecture and pipeline guidance
- `src/tmp_mock.py`, `tests/test_mock.py` – minimal examples for tests

If anything here is ambiguous, leave a short TODO comment in code referencing this file and ask the repository owner to confirm the preferred pattern.
