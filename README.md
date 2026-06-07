# Project Standards

Production-grade guardrails for software repositories: environment variables, licensing, git workflow, documentation, CI, security, and planning.

**Start here:** [GUARDRAILS.md](GUARDRAILS.md)

---

## Documents

| File | Topic |
|------|-------|
| [GUARDRAILS.md](GUARDRAILS.md) | Master index and compliance checklist |
| [AGENTS.md](AGENTS.md) | Instructions for AI agents and agent-forge |
| [01-ENV-VARIABLES.md](01-ENV-VARIABLES.md) | `.env.example`, naming, dev/prod modes |
| [02-LICENSE.md](02-LICENSE.md) | MIT or Apache-2.0 (one per repo) |
| [03-GIT-WORKFLOW.md](03-GIT-WORKFLOW.md) | Commits, branches, PRs |
| [04-README-AND-PRESENTATION.md](04-README-AND-PRESENTATION.md) | README, `presentation.html`, Pages CI |
| [05-PLANNING.md](05-PLANNING.md) | `plan.md` template and status tracking |
| [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) | Root file tree, CI, coverage gates |
| [07-SECURITY.md](07-SECURITY.md) | Auth, logging, SSRF, SQL, path traversal |

---

## Maturity tiers

| Tier | Intent |
|------|--------|
| T0 | Spike / experiment |
| T1 | Active dev, locally runnable |
| T2 | Release-ready (public, licensed, CI, demo) |
| T3 | Production deployed service |

See [GUARDRAILS.md](GUARDRAILS.md) for the full checklist per tier.

---

## Using with agent-forge

Point agent-forge (or any agent) at this repo as the standards source:

```bash
# Clone alongside your projects
git clone https://github.com/vgandhi1/standards.git

# In agent-forge goal or Cursor prompt:
# "Follow standards in ../standards/AGENTS.md when scaffolding or reviewing the repo."
```

Read [AGENTS.md](AGENTS.md) for task routing, preset mapping, and compliance gates.

---

## License

MIT — see [LICENSE](LICENSE).
