# Environment Variables Standard

**Applies to:** All runnable projects (APIs, CLIs, batch jobs, frontends with backend config)  
**Tier:** Required at **T1+**

---

## Rules

### 1. Never commit secrets

- `.env` must be listed in `.gitignore`.
- Do not commit API keys, tokens, passwords, connection strings with credentials, or private keys.
- Use placeholder values in committed files only.

### 2. Ship `.env.example`

Every repo that reads environment variables must include **`.env.example`** at the repo root (or service root in monorepos).

**Reference implementations:**

- `QualityMind-RAG/.env.example` — grouped sections, inline comments
- `Agents/agent-forge/.env.example` — copy instructions, optional vars commented out

### 3. Naming conventions

| Pattern | Example | Use for |
|---------|---------|---------|
| `{SERVICE}_` prefix | `CLAIMLENS_API_KEY`, `QUALITYMIND_BASE_URL` | Service-specific vars in multi-service setups |
| `ENVIRONMENT` or `{SERVICE}_ENVIRONMENT` | `ENVIRONMENT=development` | Runtime mode |
| `*_API_KEY` | `OPENAI_API_KEY`, `PINECONE_API_KEY` | External API credentials |
| `*_URL` / `*_BASE_URL` | `DATABASE_URL`, `QUALITYMIND_BASE_URL` | HTTP or connection endpoints |
| `*_HOST` | `AGENTFORGE_OLLAMA_HOST` | Host-only overrides (validate before use) |

**Rules:**

- Use `SCREAMING_SNAKE_CASE`.
- Prefer explicit names over generic `KEY` or `SECRET`.
- Document default values in `.env.example` comments, not only in code.

### 4. Environment modes

All services must support at least two modes:

| Value | Behavior |
|-------|----------|
| `development` | Verbose logging OK; auth optional when `*_API_KEY` unset; fail-open only where documented |
| `production` | Fail-closed auth; no debug/trace of sensitive payloads; generic client errors |

**Example (`.env.example`):**

```bash
# Application
ENVIRONMENT=development

# Optional API auth — unset in dev allows open access; REQUIRED in production
# API_KEY=
```

Load via Pydantic Settings, `python-dotenv`, or framework equivalent — not scattered raw `os.environ` without defaults documented in `.env.example`.

### 5. `.env.example` structure

Use this section order:

```bash
# ── Copy to .env and fill in values. Never commit .env ──

# ── Application ──
ENVIRONMENT=development
APP_NAME=My Service
APP_VERSION=0.1.0

# ── Authentication (optional in dev) ──
# API_KEY=

# ── External APIs ──
OPENAI_API_KEY=sk-your-key-here

# ── Database ──
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# ── Feature flags / tuning ──
# CHUNK_SIZE=512
```

**Requirements:**

- Group related vars under `# ── Section ──` headers.
- Comment every non-obvious var (purpose, allowed values, link to provider console).
- Use safe placeholders (`your-key-here`, `xxxxxxxx`).
- Comment out optional vars with `#` and show example values.

### 6. README documentation

If the README has a Configuration section, it must:

1. Point to `.env.example` as the source of truth.
2. Show the minimal copy command: `cp .env.example .env`
3. List **required vs optional** vars in a table (acceptable when `.env.example` is very large).

**Table template:**

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `ENVIRONMENT` | No | `development` | Runtime mode |
| `OPENAI_API_KEY` | Yes (for LLM features) | — | OpenAI API key |
| `API_KEY` | Prod only | unset | Bearer token for inbound API auth |

### 7. Cross-service URLs

When one repo calls another (e.g. CLaimLens → QualityMind):

```bash
QUALITYMIND_BASE_URL=http://localhost:8000
QUALITYMIND_API_KEY=
```

- Use `*_BASE_URL` without trailing slash.
- Document localhost ports in README Quick Start.
- Never hardcode production URLs in source; use env vars.

### 8. Validation in code

- Validate URLs (scheme `http`/`https`, allowed hosts in dev for SSRF-sensitive clients).
- Validate enums (`ENVIRONMENT` ∈ `development`, `staging`, `production`).
- Fail fast on startup for missing **required** vars in production.

See [07-SECURITY.md](07-SECURITY.md) for SSRF and logging rules around env-driven HTTP targets.

---

## Monorepo / multi-service

| Layout | Rule |
|--------|------|
| Single deployable | One `.env.example` at repo root |
| `backend/` + `frontend/` | `.env.example` per service (`backend/.env.example`, `frontend/.env.example`) |
| Docker Compose | Document env in `.env.example` **and** reference in `docker-compose.yml` with `${VAR}` syntax |

---

## Compliance checklist

- [ ] `.env` in `.gitignore`
- [ ] `.env.example` committed with grouped sections
- [ ] No real secrets in git history (rotate if leaked)
- [ ] `ENVIRONMENT` (or prefixed equivalent) supported
- [ ] README Configuration section links to `.env.example`
- [ ] Production-required vars enforced at startup
