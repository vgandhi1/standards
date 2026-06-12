# Agent Instructions — Project Standards

This file tells AI agents (Cursor, Claude Code, **agent-forge**, GitHub Copilot) how to use this standards repo when working on project codebases.

**Do not skip:** Load [COMPLIANCE.md](COMPLIANCE.md) for the compliance checklist before marking work complete.

---

## Relationship to the Guardrails behavior layer

This repo governs **repo hygiene only** (env, license, CI, README, security, planning). It does **not**
define how the assistant should behave. For that — Tier 1/2/3 behavior rules, the safety baseline
(read-only by default, PII redaction, escalation, grounding), and the machine-enforced `.cursor` rules —
load the sibling **Guardrails** repo's behavior layer (`Guardrails/core/CLAUDE.md` + its `AGENTS.md`).

A project adopting both keeps **one** root `AGENTS.md` carrying behavior, and loads this repo's checklist
for hygiene. The two are complementary: behavior (Guardrails) + hygiene (here). Security overlaps both —
`07-SECURITY.md` here is the canonical app-security substance; Guardrails' `.cursor` security rule mirrors
the enforceable subset.

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
| LLM calls, agent loops, RAG, SQL generation | [08-AI-SECURITY.md](08-AI-SECURITY.md) |
| Tests, coverage, CI | [09-TESTING.md](09-TESTING.md) |
| Production deploy | [07-SECURITY.md](07-SECURITY.md) prod checklist + [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) T3 |

---

## agent-forge preset mapping

agent-forge is a scaffolding and review agent. Its **presets** are named workflow stages — each maps to a specific set of standards to enforce.

| agent-forge preset | What it does | Standards to enforce |
|--------------------|--------------|----------------------|
| `intake` | Reads the repo and identifies tier, missing files, and open compliance gaps | Confirm tier (T0–T3); list missing guardrail files against [COMPLIANCE.md](COMPLIANCE.md) |
| `design` | Reviews or proposes architecture: API contracts, data flow, tech stack | Architecture in README/plan; security constraints from [07-SECURITY.md](07-SECURITY.md); AI constraints from [08-AI-SECURITY.md](08-AI-SECURITY.md) if LLMs involved |
| `implement` | Writes or reviews implementation code | Code + tests; no secrets; parameterized SQL; SSRF guards on outbound HTTP; AI output validation if LLMs used |
| `test` | Adds or reviews test coverage | CI coverage gate per tier ([06-REQUIRED-FILES.md](06-REQUIRED-FILES.md)); test organization per [09-TESTING.md](09-TESTING.md) |
| `ship` | Generates CI workflows, Dockerfile, verifies T2/T3 file requirements | Generate `.github/workflows/test.yml`, optional `pages.yml`, `Dockerfile`; verify [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) T3 checklist |
| `artifacts` | Syncs documentation state: plan, README, env example | Ensure `plan.md`, README, `.env.example` agree; `Last updated` current |

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
- [ ] AI/LLM security rules from [08-AI-SECURITY.md](08-AI-SECURITY.md) respected (if LLMs involved)
- [ ] Tests follow [09-TESTING.md](09-TESTING.md) conventions (if tests added or changed)
- [ ] `plan.md` status updated if scope changed

---

## Cursor / Claude Code setup

Add to the **project repo** (not this standards repo) `.cursor/rules/` or `CLAUDE.md`:

```markdown
## Project standards

When creating files, reviewing PRs, or scaffolding:
- Follow the standards repo (local: ../standards/)
- Start with COMPLIANCE.md compliance checklist
- License: MIT or Apache-2.0 only — one per repo ([02-LICENSE.md](../standards/02-LICENSE.md))
```

**Precedence rule:** Project-level `CLAUDE.md` rules take precedence over these standards for project-specific decisions (e.g. which linter, which test framework, which model). Standards define the floor; `CLAUDE.md` can raise it. Standards must not be used to override an explicit project-level constraint.

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
- Execute raw LLM-generated SQL — the application layer always owns query construction
- Override a project-level `CLAUDE.md` constraint using a standards default
- Set LLM temperature above 0.4 in any production code path
- Add a new LLM call site without OPIK (or equivalent) tracing
- Silently swallow LLM tool errors — propagate as structured error objects
- Commit model weights, large binary files, or real production data as test fixtures
