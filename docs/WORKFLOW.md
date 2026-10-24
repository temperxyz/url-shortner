# WORKFLOW (the full picture, start to finish)

Last updated: 2026-10-23

## What we are building
A URL shortener with a cache in front of the database, load-tested to show what the cache buys. Reads (redirects) are about 100x more common than writes (creates), and the whole design follows from that.

## Request flows

### Create a short URL
```
POST /shorten {"url": "https://example.com/very/long/path"}
  1. Validate: must be http or https, sane length
  2. Generate 7 random base62 chars
  3. INSERT into urls (short_code, long_url)
       - UNIQUE violation? generate a new code and retry (max few attempts)
  4. Return {"short_url": "<BASE_URL>/<code>"}
```

### Redirect
```
GET /{code}
  1. Redis GET code
       - HIT:  302 to the cached long URL (MySQL is never touched)
       - MISS: SELECT long_url FROM urls WHERE short_code = ?
                 - found:     Redis SET code long_url EX <TTL>, then 302
                 - not found: 404 (optionally cache the miss for a few seconds)
```

## Data model
```sql
CREATE TABLE urls (
  id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  short_code  VARCHAR(16)  NOT NULL,
  long_url    TEXT         NOT NULL,
  created_at  TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP,
  expires_at  TIMESTAMP    NULL,
  click_count BIGINT UNSIGNED NOT NULL DEFAULT 0,
  UNIQUE KEY uq_short_code (short_code)
);
```
Note: updating `click_count` on every redirect would put a DB write on the hot path and defeat the cache. Leave it unused for now. The stretch version counts in Redis and flushes to MySQL in batches.

## Planned repo layout
```
url-shortener/
  CONTEXT.md
  README.md
  app/
    main.py          FastAPI app, wires routers
    config.py        settings from .env
    db.py            SQLAlchemy engine and session
    models.py        Url table
    schemas.py       request/response models
    codegen.py       code generation strategies
    cache.py         Redis helpers
    routes/
      shorten.py
      redirect.py
  tests/
  loadtests/
    locustfile.py
  docs/              PROGRESS, TODO, DECISIONS, STACK, WORKFLOW
  docker-compose.yml
  requirements.txt
  requirements-dev.txt
  .env.example
  .gitignore
```

## Phases
| Phase | When | Outcome |
|-------|------|---------|
| 0. Setup | Day 0 (tonight) | Repo, venv, Docker, MySQL + Redis running |
| 1. Core | Day 1 | Create + redirect working against MySQL, with tests |
| 2. Cache + containers | Day 2 | Redis cache-aside, rate limiting, app in Docker |
| 3. Measure + polish | Day 3 | Locust results with/without cache, README with diagram and numbers |
| Stretch | After | Code strategy comparison, click counting, expiry, nginx, Alembic |

## Definition of done
- `POST /shorten` and `GET /{code}` work end to end
- Cache hit and miss are visible in logs
- Locust numbers for cache off vs on are in the README
- `docker compose up` starts everything
- A stranger can clone the repo and run it from the README
