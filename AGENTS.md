# Agent Instructions — Project Standards

This file tells AI agents (Cursor, Claude Code, **agent-forge**, GitHub Copilot) how to use this standards repo when working on project codebases.

**Do not skip:** Load [GUARDRAILS.md](GUARDRAILS.md) for the compliance checklist before marking work complete.

---

## When to apply these standards

| Task | Load |
|------|------|
| New repo scaffold | [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md), [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md), [02-LICENSE.md](02-LICENSE.md) |
| Add or change env vars | [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md) — update `.env.example` + README table |
| Public release / open source | [02-LICENSE.md](02-LICENSE.md), [04-README-AND-PRESENTATION.md](04-README-AND-PRESENTATION.md) |
| Commit / PR / push | [03-GIT-WORKFLOW.md](03-GIT-WORKFLOW.md) |
| README or presentation | [04-README-AND-PRESENTATION.md](04-README-AND-PRESENTATION.md) |
| Planning / roadmap | [05-PLANNING.md](05-PLANNING.md) |
| API, auth, HTTP client, file upload | [07-SECURITY.md](07-SECURITY.md) |
| Production deploy | [07-SECURITY.md](07-SECURITY.md) prod checklist + [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) T3 |

---

## agent-forge preset mapping

| agent-forge preset | Standards to enforce |
|--------------------|----------------------|
| `intake` | Confirm tier (T0–T3); list missing guardrail files |
| `design` | Architecture in README/plan; security constraints from [07-SECURITY.md](07-SECURITY.md) |
| `implement` | Code + tests; no secrets; parameterized SQL; SSRF guards on outbound HTTP |
| `test` | CI coverage gate per tier in [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) |
| `ship` | Generate `.github/workflows/test.yml`, optional `pages.yml`, `Dockerfile`; verify [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) T3 |
| `artifacts` | Ensure `plan.md`, README, `.env.example` synced |

Example goal for agent-forge:

```
Scaffold a FastAPI service at T2 (release-ready).
Follow ../standards/AGENTS.md and ../standards/06-REQUIRED-FILES.md.
Include .env.example, MIT LICENSE, test CI with 25% coverage gate, plan.md.
```

---

## Compliance gate (run before finishing)

Answer **yes** to all that apply:

- [ ] `.env` is gitignored; `.env.example` updated if env vars changed
- [ ] No secrets, API keys, or credentials in the diff
- [ ] `LICENSE` present and matches README (public repos)
- [ ] Commit message follows Conventional Commits ([03-GIT-WORKFLOW.md](03-GIT-WORKFLOW.md))
- [ ] README Quick Start commands are accurate
- [ ] Tests pass locally; CI workflow exists or was updated
- [ ] Security rules from [07-SECURITY.md](07-SECURITY.md) respected for APIs and outbound HTTP
- [ ] `plan.md` status updated if scope changed

---

## Cursor / Claude Code setup

Add to the **project repo** (not this standards repo) `.cursor/rules/` or `CLAUDE.md`:

```markdown
## Project standards

When creating files, reviewing PRs, or scaffolding:
- Follow https://github.com/vgandhi1/standards (local: ../standards/)
- Start with GUARDRAILS.md compliance checklist
- License: MIT or Apache-2.0 only — one per repo ([02-LICENSE.md](../standards/02-LICENSE.md))
```

No GitHub Actions workflow is required **in this standards repo** — it is documentation only. Individual project repos carry their own CI per [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md).

---

## File generation order (new repo)

1. `README.md` — [04-README-AND-PRESENTATION.md](04-README-AND-PRESENTATION.md)
2. `.gitignore` — [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md)
3. `.env.example` — [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md)
4. `LICENSE` — [02-LICENSE.md](02-LICENSE.md)
5. `plan.md` — [05-PLANNING.md](05-PLANNING.md)
6. `.github/workflows/test.yml` — [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md)
7. `presentation.html` + `.github/workflows/pages.yml` — if T2+ with demo deck

---

## What agents must not do

- Commit `.env` or real API keys
- Add GPL/AGPL license without explicit human approval
- Use two licenses on the same repo (pick MIT **or** Apache-2.0)
- Skip `.env.example` when adding new environment variables
- Expose stack traces or SQL errors in user-facing API responses
- Use raw user input in SQL strings or outbound HTTP URLs
