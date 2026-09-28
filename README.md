# MovieShelf

[![CI](https://github.com/MykolaDotsenko/movieshelf/actions/workflows/ci.yml/badge.svg)](https://github.com/MykolaDotsenko/movieshelf/actions/workflows/ci.yml)
![Python 3.13–3.14](https://img.shields.io/badge/Python-3.13%E2%80%933.14-3776AB?logo=python&logoColor=white)
![Django 5.2](https://img.shields.io/badge/Django-5.2-092E20?logo=django&logoColor=white)
![Coverage 97%](https://img.shields.io/badge/coverage-97%25-brightgreen)

**A Django movie catalog used to demonstrate relational modelling, PostgreSQL search, authentication and production-oriented backend checks.**

**Live static preview:** https://mykoladotsenko.github.io/movieshelf/

GitHub Pages hosts a presentation preview built from real application captures. The full product is a Django application, so database search, authentication and other server-side flows require a Python server runtime and are intentionally not simulated on Pages.

<p align="center">
  <img src="docs/screenshots/home-desktop.png" alt="MovieShelf desktop home" width="49%">
  <img src="docs/screenshots/movies-desktop.png" alt="MovieShelf movie catalog" width="49%">
</p>

<img src="docs/screenshots/home-mobile.png" alt="MovieShelf mobile home" width="390">

## What the application does

- browse movies, genres, cast and directors;
- open movie and person/filmography pages;
- search the catalog;
- sign up, sign in and sign out with Django authentication;
- run from SQLite for zero-friction evaluation or PostgreSQL for the full search path.

The demo fixture is fictional and does not redistribute movie posters or celebrity photographs.

## Backend details worth reviewing

### Relational integrity

The schema models movies, people, genres and participation roles with database-backed rules for:

- rating ranges;
- positive durations;
- unique genres;
- duplicate cast/director credits.

Important invariants do not rely only on form/UI validation.

### PostgreSQL search

SQLite keeps a simple `icontains` fallback for easy local setup.

PostgreSQL uses Django's native search primitives:

- `SearchVector`;
- `SearchQuery(search_type="websearch")`;
- `SearchRank`;
- GIN full-text index for titles;
- `pg_trgm` GIN indexes for partial title/genre matching.

CI starts a real PostgreSQL service and runs the test suite against it, so PostgreSQL support is exercised rather than only documented.

### Production configuration

Environment parsing is strict: malformed boolean/integer configuration fails fast instead of silently changing behaviour.

The app includes:

- database-backed `/health/` readiness;
- Gunicorn + WhiteNoise production configuration;
- production Django security checks;
- dependency auditing;
- Render Blueprint configuration.

## Architecture

MovieShelf intentionally stays close to Django's standard structure:

```text
URL
 ↓
View
 ↓
Model / ORM
 ↓
Database

Form     → input validation
Template → presentation
```

There is no extra repository/service layer whose only job would be to proxy Django ORM calls.

## Stack

- Python 3.13 / 3.14
- Django 5.2
- PostgreSQL / SQLite
- Bootstrap 5
- Gunicorn
- WhiteNoise
- Ruff
- Coverage
- Playwright
- axe-core
- GitHub Actions

## Quality

Python checks:

```bash
python -m pip install -r requirements-dev.txt
pip-audit -r requirements-postgres.txt
ruff check .
ruff format --check .
python manage.py makemigrations --check --dry-run
python manage.py check
coverage run manage.py test
coverage report
```

Browser checks:

```bash
npm install
npx playwright install chromium
npm run test:e2e
```

CI verifies:

- Python 3.13 and 3.14;
- migrations and demo fixture;
- real PostgreSQL compatibility;
- Django production checks;
- dependency audit;
- coverage floor;
- Chromium desktop/mobile flows;
- axe accessibility checks.

## Run locally

```bash
python -m venv .venv
python -m pip install -r requirements.txt
python manage.py migrate
python manage.py loaddata demo
python manage.py runserver
```

Open `http://127.0.0.1:8000/`.

For PostgreSQL, install `requirements-postgres.txt` and use the settings documented in `.env.example`.

## Deployment

A Render Blueprint is included for a Django web service + managed PostgreSQL deployment.

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/MykolaDotsenko/movieshelf)

A final public deployment URL is intentionally not claimed until one exists.

## Repository layout

```text
config/          settings, env parsing, project URLs, health view
imdb/            models, forms, views, templates, migrations, tests
imdb/fixtures/   deterministic fictional demo data
docs/            screenshots
```

## License

See [LICENSE](LICENSE) and [THIRD_PARTY.md](THIRD_PARTY.md).
