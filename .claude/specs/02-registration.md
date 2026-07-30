# Spec: Registration

## Overview
Implements account creation for Spendly. Currently `GET /register` renders a
form (`templates/register.html`) that posts to `/register`, but there is no
handler for the `POST` request — submitting the form does nothing. This step
wires that form up to the database layer built in Step 1 (`database/db.py`),
so a visitor can create a `users` row with a hashed password. Session-based
login (establishing who is "logged in") is out of scope here and belongs to
the upcoming login/logout step — after a successful registration, the user is
redirected to `/login` to sign in.

## Depends on
- Step 1 — Database Setup (`.claude/specs/01-database-setup.md`), which
  provides `get_db()`, `init_db()`, and the `users` table with a unique
  `email` column and `password_hash` column.

## Routes
- `GET /register` — renders the registration form — public (already exists,
  no change needed)
- `POST /register` — validates input, creates a new user, redirects to
  `/login` on success or re-renders the form with an error on failure —
  public

## Database changes
No database changes. The `users` table (`id`, `name`, `email`,
`password_hash`, `created_at`) already exists in `database/db.py` and is
sufficient for registration.

## Templates
- **Create:** none
- **Modify:** none — `templates/register.html` already has the form fields
  (`name`, `email`, `password`), an `{% if error %}` block for validation
  messages, and posts to `/register`. No template changes are required.

## Files to change
- `app.py` — replace the current `/register` view with one that accepts
  `GET` and `POST`, handles validation, inserts the new user, and redirects
  to `login` on success.

## Files to create
None.

## New dependencies
No new dependencies.

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug (`generate_password_hash`)
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Validate on the server even though the form has `required`/`type=email`
  HTML attributes (don't trust client-side validation alone):
  - `name`, `email`, `password` must all be non-empty
  - `password` must be at least 8 characters
  - `email` must not already exist in `users` (catch the `UNIQUE` constraint
    failure, e.g. `sqlite3.IntegrityError`, rather than pre-checking with a
    separate `SELECT`)
- On any validation failure, re-render `register.html` with `error` set to a
  user-facing message and preserve the tone of the existing template (do not
  change its markup)
- On success, redirect to `url_for('login')` using `redirect()` (a
  `303`/`302` redirect, not a re-render), so a page refresh doesn't
  resubmit the form
- Close every DB connection you open (`conn.close()`), including on error
  paths

## Definition of done
- [ ] Visiting `/register` still shows the existing registration form
- [ ] Submitting the form with valid name/email/password creates a row in
      the `users` table with a hashed (not plaintext) password
- [ ] After successful registration, the browser is redirected to `/login`
- [ ] Submitting with an email that already exists (e.g. `demo@spendly.com`)
      re-renders the register page with a visible error and does not create
      a duplicate row
- [ ] Submitting with a password under 8 characters re-renders the register
      page with a visible error and does not create a row
- [ ] Submitting with a missing field re-renders the register page with a
      visible error and does not create a row
- [ ] `python app.py` starts without errors and the app still serves all
      existing routes (`/`, `/login`, `/terms`, `/privacy`)
