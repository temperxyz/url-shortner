# DECISIONS

Last updated: 2026-10-23

Status key:
- **Decided**: Muhammad stated or confirmed it. Do not reopen without a strong reason.
- **Proposed**: recommended by Claude, not yet explicitly confirmed. Can be challenged.

---

### D1. Purpose: learning first, portfolio second. Decided
About 3 days, extend if interesting. When a choice is between "simple and understood" and "impressive but opaque", pick the first.

### D2. Language: Python. Decided

### D3. Web framework: FastAPI (not Streamlit). Proposed
Streamlit is for data dashboards and cannot do HTTP redirects, which is the core feature. FastAPI is async, has automatic docs at `/docs`, and is a good thing to learn.

### D4. Database: MySQL 8.4 in Docker, via SQLAlchemy 2 + PyMySQL. Proposed
Postgres would work equally well for this project. MySQL wins because Muhammad already knows it, and this project already adds four new tools (Redis, Docker, FastAPI, Locust). SQLAlchemy keeps a later switch cheap. Host port is 3307 to avoid clashing with any local MySQL on 3306.

### D5. Schema: a single `urls` table. Decided
One-to-one mapping of short code to long URL. `short_code` has a UNIQUE index.

### D6. No sharding. Decided
62^7 is about 3.5 trillion codes. No real shortener fills that, and nothing here approaches the scale where sharding matters.

### D7. Cache: Redis, cache-aside pattern, TTL + LRU eviction. Proposed
Cache exists to reduce DB load on redirects. Hit/miss is decided by whether the key exists in Redis, not by "last modified" (that is HTTP browser caching, a different thing). Short code to URL never changes, so invalidation is only needed on delete or expiry.

### D8. Short code generation: 7 random base62 chars, insert, retry on UNIQUE violation. Proposed
Unguessable, simple, collisions are rare. A sequential counter is enumerable (anyone can walk all links). Hash-and-truncate has collisions by the birthday paradox and needs retry logic anyway. Stretch goal: implement Sqids and hash strategies behind the same interface and compare.

### D9. Architecture: one FastAPI app, two route modules (shorten, redirect). Proposed
No API gateway or separate microservices for the 3-day scope. That would eat the time on networking and deployment instead of learning the core pieces. nginx in front of multiple copies is a stretch goal that demonstrates the same idea (reads scale independently).

### D10. Redirect status: 302. Proposed
301 is cached by browsers forever, which cuts load but breaks analytics and changing the target. Revisit if click counting is dropped.

### D11. Safety basics. Proposed
Only http/https URLs, reserved words blocked as codes, rate limit on `POST /shorten`.

### D12. Docker Compose runs MySQL and Redis first. App is containerized on Day 2. Proposed
The app runs locally in a venv at first so debugging is easy.

### D13. Load testing with Locust, with cache off vs on. Proposed
The numbers are the proof the cache matters. At small scale the difference may be modest, and that is fine to report honestly.

### D14. GitHub repo, secrets only in `.env` (never committed). Decided

### D15. Context files live in the repo and are updated at the end of every session. Decided
CONTEXT.md and README.md at the root, everything else in `docs/`.
