# URL Shortener

A URL shortener built to learn how read-heavy systems work: FastAPI, MySQL, Redis (cache-aside), Docker Compose, and Locust load tests.

> Work in progress. This README gets filled in as the project is built.

## How it works
- `POST /shorten` takes a long URL and returns a 7-character base62 short code.
- `GET /{code}` redirects to the original URL.
- Redirects check Redis first. On a miss, MySQL is queried and the result is cached with a TTL.

## Architecture
_Diagram goes here (Phase 3)._

## Run it locally
```bash
cp .env.example .env          # then edit the passwords
docker compose up -d          # MySQL + Redis
python -m venv .venv
# Windows: .venv\Scripts\activate    Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```
API docs at http://localhost:8000/docs

## Load test results
_Locust results, cache off vs on, go here (Phase 3)._

## What I learned / trade-offs
_Filled in at the end._

## Tech
Python, FastAPI, SQLAlchemy, MySQL 8.4, Redis 7, Docker Compose, Locust, pytest.
