# TODO

Last updated: 2026-10-23

## Next up (do these first, in order)
1. [ ] Install Docker (Docker Desktop on Windows, Docker Engine on Ubuntu) and confirm `docker --version` works
2. [ ] Install Python 3.12 or 3.13 and confirm `python --version`
3. [ ] Create the GitHub repo, copy these files in, first commit and push
4. [ ] Create the venv and run `pip install -r requirements-dev.txt`
5. [ ] Copy `.env.example` to `.env`, set real passwords, run `docker compose up -d`, confirm both containers are healthy

## Phase 1: Core (Day 1)
- [ ] Project skeleton (`app/` package, `config.py` reading `.env`)
- [ ] DB connection with SQLAlchemy, `urls` table (see WORKFLOW.md for schema)
- [ ] Base62 helper + random code generator (7 chars)
- [ ] `POST /shorten`: validate URL (http/https only), insert, retry on duplicate code
- [ ] `GET /{code}`: look up in MySQL, 302 redirect, 404 if missing
- [ ] Reserved words (`api`, `docs`, `shorten`, ...) cannot be codes
- [ ] Basic tests with pytest + httpx
- [ ] Simple HTML page with a form (stretch within the phase)

## Phase 2: Cache + containers (Day 2)
- [ ] Redis cache-aside on the redirect path (TTL + LRU)
- [ ] Decide on negative caching for unknown codes (short TTL)
- [ ] Rate limiting on `POST /shorten`
- [ ] Dockerfile for the app, add it to docker-compose
- [ ] Log cache hit/miss so you can see it working

## Phase 3: Measure + polish (Day 3)
- [ ] `loadtests/locustfile.py` (heavy on redirects, light on creates, roughly 100:1)
- [ ] Run Locust with cache OFF, then ON. Record requests/sec and p95 latency
- [ ] Put the numbers in README.md
- [ ] Architecture diagram in README.md
- [ ] Clean up, final commit

## Stretch (only if it is still interesting)
- [ ] Second code strategy (Sqids, hash-based) behind the same interface, compare them
- [ ] Click counting in Redis, flushed to MySQL periodically
- [ ] Expiry (`expires_at`) and custom aliases
- [ ] nginx in front of 2 app instances
- [ ] Alembic migrations
