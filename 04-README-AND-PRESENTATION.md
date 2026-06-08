# README and Presentation Standard

**Applies to:** All projects; full template at **T2 (Release-ready)**  
**Reference:** `QualityMind-RAG/README.md`, `AutoClaim-VLM/README.md`

---

## README principles

1. **First screen** answers: What is this? Who is it for? How do I run it?
2. **No personal bios** in technical README — keep product/engineering focus (see agent-forge github-repository guidance).
3. Link to live demo when available.
4. Keep Quick Start copy-pasteable and tested.

---

## Required sections by tier

### T0 — Spike (minimum)

```markdown
# Project Name
One-line description.

## Quick start
<commands that work>
```

### T1 — Dev (active project)

| Section | Required |
|---------|:--------:|
| Title + one-line tagline | ✅ |
| What is this / problem statement | ✅ |
| Quick start (install + run) | ✅ |
| Configuration (link to `.env.example`) | ✅ |
| Tests (`pytest`, etc.) | ✅ |
| License | ✅ if public |

### T2 — Release-ready (public demo)

All T1 sections, plus:

| Section | Required |
|---------|:--------:|
| Live presentation link | ✅ |
| Architecture diagram (ASCII or Mermaid) | ✅ |
| Tech stack badges | ✅ |
| API overview or key commands table | ✅ if API |
| Roadmap or status table | ✅ |
| License | ✅ |

---

## README template (T2)

```markdown
<div align="center">

# Project Name

### One-line value proposition

### ▶ [**Live presentation**](https://vgandhi1.github.io/<repo>/presentation.html) · [GitHub](https://github.com/vgandhi1/<repo>)

[![Python 3.12+](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
<!-- add stack-specific badges -->

</div>

---

## What Is This?

2–3 paragraphs: problem, solution, key capabilities.

---

## Architecture

<!-- Mermaid or ASCII diagram -->

---

## Quick Start

### Prerequisites
- Python 3.12+
- Docker (optional)

### Setup
\`\`\`bash
git clone https://github.com/vgandhi1/<repo>.git
cd <repo>
cp .env.example .env   # fill in keys
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
\`\`\`

---

## Configuration

See [.env.example](.env.example). Key variables:

| Variable | Required | Description |
|----------|----------|-------------|
| `ENVIRONMENT` | No | `development` (default) or `production` |

---

## API / Usage

<!-- endpoints, CLI commands, or examples -->

---

## Tests

\`\`\`bash
pytest
\`\`\`

---

## Roadmap

| Status | Item |
|--------|------|
| ✅ | Feature A |
| 🔄 | Feature B in progress |
| ⏳ | Feature C planned |

---

## License

MIT — see [LICENSE](LICENSE).
```

---

## Badges

Use flat-square style consistently. Include only badges that reflect real dependencies:

- Language/runtime (Python, Go, Node)
- Primary framework (FastAPI, React)
- Key infra (AWS, PostgreSQL, Pinecone)
- CI status (optional): `![CI](https://github.com/vgandhi1/<repo>/actions/workflows/test.yml/badge.svg)`

Do not add vanity badges.

---

## presentation.html

Public repos with a demo deck should ship **`presentation.html`** at repo root — a self-contained slide deck or scroll narrative for GitHub Pages.

**Exception:** Skip `presentation.html` when the repo is a private utility, internal library, or scaffolding tool with no public demo. Note the exception in `plan.md` under Scope so it is intentional, not overlooked.

### Content guidelines

- Problem → approach → architecture → demo → results → roadmap
- Self-contained (inline CSS or single asset folder; no build step required for Pages)
- Link from README header: `Live presentation`
- No secrets, internal URLs, or client data in slides

### Existing examples

Repos with working `presentation.html`: QualityMind-RAG, AutoClaim-VLM, CLaimLens, aegis, SentinelFlow, ecommerce-demand-forecast

---

## GitHub Pages deployment

Add `.github/workflows/pages.yml`:

```yaml
name: Deploy presentation to GitHub Pages

on:
  push:
    branches: [main, master]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload presentation
        uses: actions/upload-pages-artifact@v3
        with:
          path: .

      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

**Alternative:** `peaceiris/actions-gh-pages@v4` publishing to `gh-pages` branch (used in SentinelFlow, aegis).

### Pages URL convention

```
https://vgandhi1.github.io/<repo-name>/presentation.html
```

Enable Pages in repo Settings → Pages → Source: GitHub Actions (or `gh-pages` branch).

---

## GitHub repository metadata

For public repos, set via GitHub UI or `gh repo edit`:

| Field | Guidance |
|-------|----------|
| Description | One technical line (see agent-forge example) |
| Website | Pages URL or leave blank |
| Topics | 5–15 relevant tags (`python`, `fastapi`, `rag`, etc.) |

---

## Compliance checklist

- [ ] README matches tier requirements
- [ ] Quick Start commands verified locally
- [ ] Configuration points to `.env.example`
- [ ] `presentation.html` exists (T2)
- [ ] Live presentation link in README (T2)
- [ ] Pages CI workflow exists (T2)
- [ ] License section links to `LICENSE`
