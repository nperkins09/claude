# CLAUDE.md

## Project Settings (edit per project)

- Python: `3.12`+ · Coverage target: `85`
- Tests required: `no` (`no` = one smoke test until asked)
- CLI framework: `none` (configure via env vars; no argparse unless named here)
- Database: `none`

## Project Layout

- docs/
  - README.md
- src/
  - main.py
- tests/
  - test_main.py
.env.example

## AI Working Rules

**Do**
- Ask clarifying questions when requirements are ambiguous.
- Follow nearby patterns. Make minimal, focused changes.
- Run the validation gate after every change and report the result.
- Update the README.md after editing the code.

**Don't**
- Refactor unrelated code.
- Use `# type: ignore` / `# noqa` without a specific code and a reason.
- Edit applied migrations, `generated/`, or `vendor/`.
- Hardcode secrets or commit `.env` / `.venv/`.
- Don't build the test until told to.
- Don't use argparse unless told to do so.

## Logging

- Interpolation uses `%s`, never f-strings: `logger.info("Starting %s", name)`.
- In `except` blocks use `logger.exception(...)`. Log an error once, where it's handled.
- Levels: DEBUG diagnostics · INFO lifecycle and business events · WARNING handled
  surprise · ERROR operation failed · CRITICAL cannot continue.
- `LOG_FORMAT=text` locally, `json` in production. UTC ISO 8601 timestamps, output to stderr.
- Never log secrets, tokens, request bodies, or personal data.
- Make the log output always have the following format: "%(asctime)s %(levelname)-8s %(name)s:%(lineno)d %(funcName)s() - %(message)s",

## Testing

- Every behavior change ships with tests (when tests are required).
- `test_<unit>_<behavior>[_<condition>]`; one behavior per test; Arrange / Act / Assert.
- Use `tmp_path` for files, `monkeypatch` for env, `caplog` for log assertions,
  and `mocker` at the `adapters/` boundary. Never call real networks or services.
- Assert outcomes, not internals. Use `pytest.raises(..., match=...)`.
- Warnings are errors. Coverage below the target fails the run.
- Synthetic data only: no PII, secrets, or real user data.
- Never delete, skip, `xfail`, or weaken a test to make the build pass.

## Documentation

Every project answers four questions. Each answer has one home; link, don't duplicate.

| # | Question | Where | Must contain |
|---|----------|-------|--------------|
| 1 | What does this project do? | `docs/README.md` → `## Overview` | 2–4 sentences: the problem, who it's for, inputs → outputs, and what it does *not* do |
| 2 | How does it work? | `docs/README.md` → `## How It Works` | 3–7 numbered steps of the main flow in plain language, naming the modules involved; link to ARCHITECTURE.md |
| 3 | How do I set up / run it? | `docs/README.md` → `## Setup`, `## Usage`, `## Configuration`, `## Development` | Prerequisites; `uv sync` and `cp .env.example .env`; exact run command with sample output; config table (variable, required, default, description); the validation gate |
| 4 | What is the architecture? | `docs/README.md` | Purpose and scope; components table (module → responsibility); data-flow diagram (Mermaid or ASCII); external services and dependencies (→ why); key decisions (decision, reason, date); open questions |

- Write for a new teammate with no context: plain language, short sentences, no marketing.
- Every documented command must be run and work before it is written down.
- Update docs in the same change as the code they describe.
- Docstrings say what a function does and how to call it; docs say why and how parts fit together. Don't paste code into docs.
- Placeholders only: no real hostnames, credentials, or personal data.
- One-off projects still answer all four questions.

## Coding Standards & Conventions

- Type hints on every signature; modern syntax (`list[str]`, `X | None`).
- Google-style docstrings on public modules, classes, functions.
- Separate logic from I/O. Pass dependencies as arguments; no hidden globals.
- Frozen, slotted dataclasses or Pydantic models instead of dicts for structured data.
- `pathlib.Path`; always `encoding="utf-8"` for text files.
- Catch specific exceptions; re-raise with `from err`. Custom errors end in `Error`.
- All config goes through `Settings`. Every variable also goes in `.env.example`
  and the README config table.
- Timeouts on every network call. Parameterized SQL only.
- Comments explain *why*. No commented-out code.

## Definition of Done

1. Validation gate passes.
2. Tests added or updated.
3. New env vars are in `Settings`, `.env.example`, and the README.
4. README answer the four documentation questions correctly.
