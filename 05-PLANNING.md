# Planning Documents Standard

**Applies to:** Active and evolving projects (**T1+**)  
**Primary file:** `plan.md` at repo root (preferred)

---

## Why a plan file

- Separates **intent and status** from user-facing README.
- Tracks what is built vs planned without cluttering README.
- Gives AI assistants and collaborators a single source for scope.

---

## File naming

| Priority | Path | When |
|----------|------|------|
| **Preferred** | `plan.md` | Default for all new repos |
| Acceptable | `docs/PLAN.md` | Docs-heavy repos (SentinelFlow pattern) |
| Acceptable | `SPEC.md` | Factory / ops sub-repos with executable spec style |
| Legacy | `forecast-upgrade.md`, `docs/agents_plan.md` | Rename to `plan.md` when touching docs |

**Rule:** One canonical plan per repo. Do not split status across multiple plan files without cross-links.

---

## plan.md template

```markdown
# <Project Name> — Implementation Plan

**Status:** Active development | Maintenance | Planning  
**Last updated:** YYYY-MM-DD  
**Tier:** T1 Dev | T2 Release-ready | T3 Production

---

## Goal

One paragraph: what this project must achieve and for whom.

---

## Current status

| Area | Status | Notes |
|------|--------|-------|
| Core API | ✅ Done | FastAPI on port 8000 |
| Eval harness | 🔄 In progress | `evaluate.py` WIP |
| Production deploy | ⏳ Deferred | After dev baselines |

**Legend:** ✅ done · 🔄 in progress · ⏳ planned · ❌ blocked · ☁️ deployed

---

## Scope

### In scope (this phase)
- Item 1
- Item 2

### Out of scope (deferred)
- Production hardening
- Item X

---

## Milestones

### M1 — Dev analytical baseline
- [ ] Runnable locally with `ENVIRONMENT=development`
- [ ] Eval script produces `metrics.json` / manifest
- [ ] Tests pass in CI

### M2 — Integration demo
- [ ] End-to-end localhost demo documented
- [ ] Cross-service handoff tested

### M3 — Release-ready
- [ ] Guardrails compliance (see GUARDRAILS.md)
- [ ] Presentation updated

---

## Architecture decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Vector store | Pinecone | Managed, existing index |
| Auth | Optional API key in dev | Fail-closed in prod |

---

## Dependencies on other repos

| Repo | Integration | Status |
|------|-------------|--------|
| QualityMind-RAG | CLaimLens handoff → `/quality/five-why` | ✅ client exists |

---

## Open questions

- Q1: …

---

## Change log

| Date | Change |
|------|--------|
| YYYY-MM-DD | Initial plan |
```

---

## Status values

Use consistently across plan and README roadmap tables:

| Symbol | Meaning |
|--------|---------|
| ✅ | Done and merged |
| 🔄 | Work in progress (local or branch) |
| ⏳ | Planned, not started |
| ❌ | Blocked — note blocker |
| ☁️ | Deployed to remote / pushed |

Update **`Last updated`** on every meaningful plan edit.

---

## Relationship to other docs

| Doc | Role |
|-----|------|
| `README.md` | User-facing; high-level roadmap table only |
| `plan.md` | Detailed status, milestones, decisions |
| `docs/ROADMAP.md` | Optional long-horizon vision |

When README roadmap and plan diverge, **plan.md wins** — sync README after plan updates.

---

## Dev vs production phases

For ML / analytical repos, explicitly separate phases:

```markdown
## Phase: Dev analytical (current)
Focus: train, evaluate, iterate, demo locally.

## Phase: Production (deferred)
Do not prioritize until dev exit criteria met:
- [ ] RAGAS / holdout metrics documented
- [ ] Fail-closed auth
- [ ] Rate limiting
```

---

## Compliance checklist

- [ ] `plan.md` (or approved alternate) exists at T1+
- [ ] Status table reflects reality (not stale "Planning" when code exists)
- [ ] Milestones have checkboxes
- [ ] `Last updated` date is current
- [ ] README roadmap synced with plan status
