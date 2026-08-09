# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Spendly** — a personal expense tracker (rupee-based, ₹) built as a Flask web app. It is structured as a **step-by-step learning project**: the scaffold (routes, templates, styling) is complete and wired, but the data layer and feature logic are intentionally left as stubs to be implemented in numbered steps. Treat stub comments and the `"coming in Step N"` placeholders as the spec for what to build.

## Directory layout (important)

This repo (the inner `expense-tracker/` folder) is nested inside an outer `expense-tracker/` folder that holds the virtualenv and is often the shell's working directory:

```
expense-tracker/              ← outer folder; often the shell's cwd, NOT the repo
├── venv/                     ← virtualenv (outside the repo)
├── file.md                   ← transcript of a past session, not project code
└── expense-tracker/          ← THIS repo: git root + all app files
    ├── app.py
    ├── database/
    ├── templates/
    └── static/
```

If the shell starts in the outer folder, `cd expense-tracker` before running the app, git, or pytest.

## Commands

Run from this repo folder:

```bash
python app.py                          # dev server on http://127.0.0.1:5001 (port 5001, not 5000; debug=True)
pip install -r requirements.txt        # flask, werkzeug, pytest, pytest-flask
pytest                                 # no tests exist yet; pytest + pytest-flask are already deps
pytest path/to/test_file.py::test_name # run a single test
```

Note: past sessions installed dependencies into the **global** Python 3.14 rather than the `venv/` (activation didn't stick on this Windows shell). To force the venv, call its interpreter directly: `../venv/Scripts/python.exe -m pip install -r requirements.txt`.

## Architecture

Classic server-rendered Flask app; request flow is `app.py` route → `render_template(...)` → Jinja2 template pulling from `static/`.

- **`app.py`** — the entry point and the single place all routes are defined. Currently: `/`, `/register`, `/login`, `/terms`, `/privacy` render templates; `/logout`, `/profile`, `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete` return plain-text placeholders naming the step that will implement them.
- **`templates/`** — Jinja2 templates using inheritance. `base.html` is the shell (nav, footer, font imports, global CSS link) and defines the `title`, `head`, `content`, and `scripts` blocks. Every page does `{% extends "base.html" %}` and fills in blocks — never duplicate the nav/footer.
- **`static/`** — `css/style.css` is global (linked in `base.html`); per-page CSS is loaded through the `{% block head %}` slot (e.g. `landing.html` pulls in `css/landing.css`). Page-specific JavaScript goes in `{% block scripts %}`; `js/main.js` (loaded globally) is an empty stub for shared client code.
- **`database/`** — the data layer, kept as an importable package (`from database.db import get_db`). `db.py` is a stub whose comments specify the three functions to build in Step 1: `get_db()` (SQLite connection with `row_factory` and foreign keys enabled), `init_db()` (`CREATE TABLE IF NOT EXISTS`), and `seed_db()` (sample dev data). The SQLite file is `expense_tracker.db` (gitignored, created by `init_db()`).

## Conventions

- Design system: DM Serif Display + DM Sans (Google Fonts), currency is the rupee (₹). Match the existing class-naming style (BEM-ish, e.g. `hero-stat-value`, `feature-card`) when adding markup.
- Implement stubbed routes and files in place; the step numbers in `app.py` indicate build order (Step 1 = database, Step 3 = logout, Step 4 = profile, Steps 7–9 = expense add/edit/delete).
