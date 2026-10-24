# CONTEXT (read this first)

Paste this file at the start of any new chat, then the files listed under "Reading order". The goal is that the assistant knows exactly where the project stands instead of guessing.

Last updated: 2026-10-23

## The project
A URL shortener. `POST /shorten` takes a long URL and returns a short code (7 chars, base62). `GET /<code>` redirects to the long URL. Redirects vastly outnumber creates, so redirects check a Redis cache before touching MySQL.

Built by Muhammad mainly to **learn**. Portfolio is the secondary goal. Time budget: about 3 days, extendable if it stays interesting.

## About the developer
- Third-year CS student. Comfortable with Python and MySQL.
- First time using Redis, Docker, FastAPI and Locust. Explain the "why" behind these when they come up, not just the commands.
- OS: (fill in: Windows / Ubuntu)
- Python version in use: (fill in)

## Rules for the assistant
1. Read PROGRESS.md and TODO.md before suggesting anything.
2. Do not reopen anything marked **Decided** in DECISIONS.md. Items marked **Proposed** can be challenged.
3. If something in these files looks wrong or outdated, say so instead of guessing.
4. Be honest about trade-offs. Scope is small on purpose, so push back on over-engineering.
5. At the end of every session, output updated versions of PROGRESS.md, TODO.md and DECISIONS.md (only the ones that changed) so they can be pasted into the repo.

## Reading order
1. CONTEXT.md (this file)
2. PROGRESS.md (what is done)
3. TODO.md (what is next)
4. DECISIONS.md (what is settled and why)
5. STACK.md (tools, versions, ports)
6. WORKFLOW.md (the full picture, start to finish)

README.md is public-facing for GitHub. Only read it when working on it.
