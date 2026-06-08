# Project Guardrails

**Scope:** Every project repository in the workspace  
**Last updated:** 2026-06-07

These guardrails define minimum standards for **production-grade** software projects — from local development through public release and deployed services.

---

## Quick compliance checklist

Use this before opening a PR or publishing a repo publicly.

| # | Requirement | Standard doc | Required for |
|---|-------------|--------------|--------------|
| 1 | `.env.example` with grouped, commented vars | [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md) | All runnable projects |
| 2 | `.env` in `.gitignore`; no secrets in git | [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md) | All projects |
| 3 | `LICENSE` file (MIT or Apache-2.0 — **one per repo**) | [02-LICENSE.md](02-LICENSE.md) | Public / open-source repos |
| 4 | Conventional Commits; branch naming | [03-GIT-WORKFLOW.md](03-GIT-WORKFLOW.md) | All git repos |
| 5 | README with required sections | [04-README-AND-PRESENTATION.md](04-README-AND-PRESENTATION.md) | All projects |
| 6 | `presentation.html` + GitHub Pages workflow | [04-README-AND-PRESENTATION.md](04-README-AND-PRESENTATION.md) | Public repos with demo decks |
| 7 | `plan.md` (or `docs/PLAN.md`) with status | [05-PLANNING.md](05-PLANNING.md) | Active / evolving projects |
| 8 | `.gitignore`, CI, dependency lockfile | [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) | All git repos |
| 9 | Security baseline (auth, SSRF, SQL, logs) | [07-SECURITY.md](07-SECURITY.md) | Services with APIs or outbound HTTP |
| 10 | AI/LLM security (prompt injection, output validation, agent loop, RAG) | [08-AI-SECURITY.md](08-AI-SECURITY.md) | Any project calling an LLM API or running an agent |
| 11 | Testing (offline tests, mocking, coverage, eval harness) | [09-TESTING.md](09-TESTING.md) | All projects with runnable code (T1+) |

---

## Standards index

| Document | What it covers |
|----------|----------------|
| [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md) | Naming, `.env.example`, dev vs prod, secrets |
| [02-LICENSE.md](02-LICENSE.md) | MIT or Apache-2.0 (pick one), file placement |
| [03-GIT-WORKFLOW.md](03-GIT-WORKFLOW.md) | Commits, branches, push, PRs, release tags |
| [04-README-AND-PRESENTATION.md](04-README-AND-PRESENTATION.md) | README template, badges, `presentation.html`, Pages |
| [05-PLANNING.md](05-PLANNING.md) | `plan.md` structure, status tracking, roadmap |
| [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) | Root file tree, CI, deps, `.gitignore` |
| [07-SECURITY.md](07-SECURITY.md) | Auth, logging, SSRF, SQL, path traversal |
| [08-AI-SECURITY.md](08-AI-SECURITY.md) | Prompt injection, LLM output validation, agent loop safety, RAG grounding, observability, cost controls |
| [09-TESTING.md](09-TESTING.md) | Test types, directory structure, mocking strategy, coverage gates, eval harness, fixtures |

---

## Repo maturity tiers

Match tier to project intent. Production-grade work targets **T2+**.

| Tier | When | Minimum files |
|------|------|---------------|
| **T0 — Spike** | Throwaway experiment, < 1 week | `README.md`, `.gitignore` |
| **T1 — Dev** | Active development, local runnable | T0 + `.env.example`, `plan.md`, CI test job |
| **T2 — Release-ready** | Public repo, documented, demo-ready | T1 + `LICENSE`, `presentation.html`, Pages CI, coverage gate |
| **T3 — Production** | Deployed service with SLAs | T2 + [07-SECURITY.md](07-SECURITY.md) prod checklist, deploy CI, observability |

Default branch: **`main`** for new repos. Existing repos may keep `master`; do not rename without explicit need.

---

## Multi-repo containers

Some top-level directories hold **multiple git repos** under one folder — they are not themselves a repo.

**Rule:** Each sub-repo inside a container folder follows these guardrails independently. The container folder is not subject to these guardrails directly.

**Recommended:** Add a minimal `README.md` at the container root with a table listing sub-repos, their tier, and links. This makes the portfolio navigable without opening each sub-repo.

```markdown
# <Domain or Portfolio Name>

| Repo | Tier | Description |
|------|------|-------------|
| `service-a/` | T2 | Short description |
| `service-b/` | T1 | Short description |
```

Do not add a root `.gitignore`, `LICENSE`, or CI workflow to the container folder — those belong to each sub-repo.

---

## How to adopt in an existing repo

1. Read the tier that matches your project (T0–T3).
2. Walk the [Quick compliance checklist](#quick-compliance-checklist).
3. Copy templates from the linked standard docs.
4. Open a single PR titled `docs: align repo with project guardrails`.
5. Update `plan.md` status table to mark guardrail items done.

---

## Agentic tooling

AI agents (Cursor, Claude Code, **agent-forge**) should load [AGENTS.md](AGENTS.md) before modifying any project repo. That file maps tasks to the correct standard doc and compliance checks.
