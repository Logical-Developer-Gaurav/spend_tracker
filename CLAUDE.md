# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Spendly — a personal expense-tracking web app built with Flask, server-rendered
Jinja templates, and vanilla CSS/JS. This is a step-by-step learning project (see
comments in `database/db.py` — "Students will write this file in Step 1", etc.),
so much of the backend (database layer, auth, expense CRUD) is intentionally
unimplemented placeholder code, to be filled in incrementally.

## Commands

```bash
# activate the virtualenv first (Windows)
venv\Scripts\activate

pip install -r requirements.txt   # install dependencies
python app.py                     # run the dev server (http://localhost:5001, debug=True)
pytest                            # run tests
pytest path/to/test_file.py::test_name   # run a single test
```

There is no build step or linter configured — this is a plain Flask app with
server-rendered templates.

## Architecture

- **`app.py`** — single Flask app with all routes. Currently implemented:
  `/` (landing), `/register`, `/login`, `/terms`, `/privacy`. Several routes
  (`/logout`, `/profile`, `/expenses/add`, `/expenses/<id>/edit`,
  `/expenses/<id>/delete`) are explicit placeholders returning plain strings —
  these are meant to be built out later (auth, session handling, DB-backed
  CRUD) rather than treated as dead code to clean up.
- **`database/db.py`** — intended home for `get_db()` (SQLite connection with
  `row_factory` and foreign keys enabled), `init_db()` (schema creation via
  `CREATE TABLE IF NOT EXISTS`), and `seed_db()` (sample data). Not yet
  implemented — currently just a comment describing the contract.
- **`templates/`** — Jinja templates. `base.html` defines the shared layout
  (nav + footer) that every page extends via `{% extends "base.html" %}` and
  fills in via `{% block content %}`. Nav/footer links use `url_for(...)`
  against route function names in `app.py`, not hardcoded paths.
- **`static/css/style.css`** — single stylesheet for the whole app, organized
  in commented sections (Navbar, Hero, Auth, Footer, Legal pages, Responsive,
  etc.). CSS custom properties (`--ink`, `--ink-muted`, `--ink-faint`,
  `--accent`, `--accent-2`, `--paper`, ...) are defined near the top of the
  file and reused throughout — prefer them over new hardcoded colors.
- **`static/js/main.js`** — currently empty; placeholder for future
  client-side behavior.
- The DB file (`expense_tracker.db`) and `venv/` are gitignored and expected
  to be created/managed locally, not committed.
