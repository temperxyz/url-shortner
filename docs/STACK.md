# STACK

Last updated: 2026-10-23

Versions below were test-installed together in a clean venv on Python 3.13 (Linux). Not yet tested on Muhammad's machine.

## Tools
| Layer | Choice | Version | Why |
|-------|--------|---------|-----|
| Language | Python | 3.12 or 3.13 | Known language. Avoid very new Python releases, wheels for some packages lag. |
| Web framework | FastAPI | 0.161.1 | Async, auto docs at /docs, simple routing |
| Server | Uvicorn | 0.54.0 | Runs FastAPI |
| Database | MySQL (Docker) | 8.4 | Known, relational, one table is enough |
| ORM | SQLAlchemy | 2.1.6 | Standard Python ORM, makes DB switch cheap |
| DB driver | PyMySQL | 1.2.3 | Pure Python MySQL driver |
| DB auth helper | cryptography | 50.0.2 | PyMySQL needs it for MySQL 8 default auth |
| Cache | Redis (Docker) | 7-alpine | In-memory key-value store, TTL, LRU eviction |
| Redis client | redis-py | 8.1.0 | Python client |
| Config | pydantic-settings | 2.16.1 | Typed settings from `.env` |
| Env loading | python-dotenv | 1.2.4 | Reads `.env` |
| Code strategy (stretch) | sqids | 0.5.2 | Non-sequential ID encoding, for comparison |
| Migrations (stretch) | Alembic | 1.20.1 | Schema versioning |
| Load testing | Locust | 2.47.0 | Python-written load tests, web UI |
| Tests | pytest + httpx | 9.1.1 / 0.28.1 | httpx is the client FastAPI's test tools use |
| Containers | Docker + Compose | any recent | Runs MySQL and Redis (and later the app) |
| Gateway (stretch) | nginx | latest | Load balance across app copies |

## Ports
| Service | Port | Note |
|---------|------|------|
| FastAPI app | 8000 | `/docs` for interactive API docs |
| MySQL | 3307 (host) to 3306 (container) | 3307 avoids clashing with a local MySQL |
| Redis | 6379 | |
| Locust UI | 8089 | |

## Architecture
```
Browser / Locust
      |
      v
  FastAPI app  ---- POST /shorten ----> generate code ----> MySQL (INSERT)
      |
      +---------- GET /{code} --> Redis GET
                                    |-- hit  --> 302 redirect
                                    |-- miss --> MySQL SELECT --> Redis SET (TTL) --> 302 redirect
```

## Environment variables (see `.env.example`)
`MYSQL_ROOT_PASSWORD`, `MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD`, `DATABASE_URL`, `REDIS_URL`, `BASE_URL`, `CACHE_TTL_SECONDS`, `CODE_LENGTH`
