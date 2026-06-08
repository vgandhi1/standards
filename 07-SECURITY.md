# Security Baseline Standard

**Applies to:** Services with HTTP APIs, databases, file I/O, or outbound HTTP (**T1+**; production checklist at **T3**)  
**Aligns with:** Workspace Cursor rules (logging, SSRF, SQL, path traversal, auth, CSRF/XSS)

---

## Dev vs production posture

| Control | Development | Production |
|---------|-------------|------------|
| API auth (`API_KEY`) | Optional when unset | **Required** — fail closed |
| Error responses | May include detail locally | Generic to clients; detail in server logs only |
| Debug logging | Allowed without secrets | No PII/secrets at any level |
| Rate limiting | Optional | Required on public endpoints |
| CORS | Permissive localhost | Explicit allowlist |

Set via `ENVIRONMENT=development|production` (see [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md)).

---

## Authentication and authorization

- Verify identity on every protected endpoint before business logic.
- Do not trust client flags or URL params for auth state.
- Enforce object-level access (no IDOR) using server-side ownership checks.
- Optional API key pattern (standard):

```python
# Dev: API_KEY unset → allow
# Prod: API_KEY unset → reject all protected routes
```

- Session cookies: `Secure`, `HttpOnly`, `SameSite=Lax` minimum.

---

## Logging

**Never log:** passwords, API keys, OAuth tokens, full auth headers, credit card numbers, full request bodies that may contain secrets.

**Safe to log:** request IDs, hashed/truncated user IDs, operation outcomes, latency.

**Client errors:** generic messages only — no stack traces, file paths, or SQL errors in API responses.

---

## SQL

- **Always** use parameterized queries / prepared statements.
- Never interpolate user input into SQL strings.
- Use least-privilege DB roles (read-only where possible).
- Do not return raw database errors to clients.

---

## SSRF and outbound HTTP

When user input influences outbound URLs:

1. Do not use raw user input as the request target.
2. Validate and sanitize URLs before requests.
3. Resolve hostnames and block private/link-local IP ranges unless explicitly allowed for dev.
4. Allow only `http`/`https` and safe ports (80, 443).
5. Do not forward internal auth headers to external hosts.

Apply these rules to any HTTP client that accepts a URL derived from user input or configuration.

---

## Path traversal (file I/O)

- Never build file paths from raw user input.
- Use allowlist validation for filenames (`^[a-zA-Z0-9._-]+$`).
- Resolve final path and confirm it stays within intended directory (`pathlib.Path.resolve()`).
- Generate safe upload filenames (UUID) instead of trusting original names.

Apply these rules to any endpoint that accepts file uploads or constructs file paths from input.

---

## CSRF and XSS (web UI)

- Cookie-based sessions: CSRF protection on state-changing requests.
- Never render untrusted input with `innerHTML` / `dangerouslySetInnerHTML` without sanitization.
- Set CSP, `X-Content-Type-Options: nosniff`, `X-Frame-Options` on HTML responses.

---

## Dependency scanning

Pin dependencies to exact versions in production (apps) and allow ranges only in libraries.

| Stack | Scan tool | How to run |
|-------|-----------|------------|
| Python | `pip-audit` | `pip install pip-audit && pip-audit` |
| Node | `npm audit` | `npm audit --audit-level=high` |
| Go | `govulncheck` | `govulncheck ./...` |

Enable **Dependabot** on every public GitHub repo (Settings → Security → Dependabot alerts). For T3 projects, add a `dependabot.yml` to automate version PRs:

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
```

Never merge a dependency update without checking the changelog for breaking changes.

---

## Secrets management

| Do | Don't |
|----|-------|
| Store secrets in `.env` (gitignored) | Commit `.env` |
| Use env vars / secret manager in prod | Hardcode keys in source |
| Rotate keys if leaked | Ignore git history exposure |
| Document vars in `.env.example` | Put real keys in README or slides |

---

## Production checklist (T3)

Before any production deploy:

- [ ] `ENVIRONMENT=production` enforced
- [ ] `API_KEY` (or equivalent) required on protected routes
- [ ] Rate limiting on public endpoints
- [ ] Parameterized SQL everywhere
- [ ] SSRF guards on outbound HTTP clients
- [ ] Upload/path handling validated
- [ ] Generic client error messages
- [ ] No secrets in logs or responses
- [ ] Dependencies scanned for CVEs (`pip-audit` for Python; `npm audit` for Node; Dependabot alerts enabled on GitHub) and pinned
- [ ] HTTPS only; secure cookie flags

---

## Compliance checklist (T1 dev minimum)

- [ ] No secrets in git
- [ ] `.env.example` documents auth vars
- [ ] SQL uses parameterized queries
- [ ] User-controlled URLs validated before HTTP
- [ ] File uploads sanitized
- [ ] Production checklist tracked in `plan.md` as deferred items
