# Repository Guidelines

## Project Structure & Module Organization

This repository contains an Apache Airflow data-quality lab. Student requirements and implementation guidance live in `assignment/`; grading criteria are in `grading/rubric.md`. The working application is under `starter_project/`:

- `dags/`: Airflow DAG definition and task wiring.
- `src/`: shared configuration and CSV validation logic.
- `scripts/`: local command-line validation runner.
- `data/`: passing and intentionally failing CSV fixtures.
- `expected/`: reference JSON summaries for manual comparison.

Root-level `Overview.txt`, `knowledge.txt`, and `Note.txt` provide Vietnamese project documentation. Generated output belongs in `starter_project/output/` and must not be written by hand.

## Build, Test, and Development Commands

The project has no build step. Run validation from the repository root:

```bash
python starter_project/scripts/run_local_check.py starter_project/data/orders_passed.csv --skip-discord
python starter_project/scripts/run_local_check.py starter_project/data/orders_failed.csv --allow-failure --skip-discord
```

The first command should produce a `passed` summary. The second preserves and displays the expected `failed` summary without treating that fixture as an unexpected CLI failure. To verify failure handling, omit `--allow-failure`; the command should exit unsuccessfully after writing the summary. Airflow should discover and manually trigger DAG ID `sales_data_quality_pipeline`.

## Coding Style & Naming Conventions

Use Python 3 type hints, four-space indentation, and standard-library modules where practical. Follow existing naming: `snake_case` for functions and variables, `UPPER_CASE` for constants, and descriptive task IDs such as `validate_orders`. Prefer `pathlib.Path` over string path manipulation. Keep validation logic in `src/validation.py`; DAG files should only orchestrate it. No formatter or linter is currently configured, so match the surrounding PEP 8 style and keep imports grouped.

## Testing Guidelines

There is no automated test framework or coverage threshold. Exercise both CSV fixtures and compare the generated JSON with the matching file in `starter_project/expected/`. Confirm all three rules independently: missing `customer_id`, non-positive or non-numeric `amount`, and status outside `completed`, `pending`, or `cancelled`. A failing validation must still leave `validation_summary.json` available.

## Commit & Pull Request Guidelines

Use concise, imperative commit subjects consistent with history, for example `Add sales data quality checks` or `Refactor DAG task wiring`. Pull requests should summarize behavior changes, list validation commands and outcomes, mention any Airflow configuration assumptions, and link related issues when applicable. Do not include generated output or webhook credentials.

## Security & Configuration

Provide `DISCORD_WEBHOOK_URL` and `AIRFLOW_INPUT_FILE` through environment variables. Never commit real webhook URLs. Use `--skip-discord` for local checks that should not make network requests.
