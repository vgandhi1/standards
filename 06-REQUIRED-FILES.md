# Required Files and Repository Structure

**Applies to:** All git repositories  
**See also:** [COMPLIANCE.md](COMPLIANCE.md) maturity tiers

---

## Root file tree by tier

### T0 — Spike

```
repo/
├── README.md
└── .gitignore
```

### T1 — Dev

```
repo/
├── README.md
├── plan.md
├── .gitignore
├── .env.example          # if env vars used
├── requirements.txt      # or pyproject.toml, package.json, go.mod
├── .github/
│   └── workflows/
│       └── test.yml      # or ci.yml
└── <source directories>
```

### T2 — Release-ready

Everything in T1, plus:

```
repo/
├── LICENSE
├── presentation.html
├── .github/
│   └── workflows/
│       ├── test.yml
│       └── pages.yml
└── tests/                # or test/
```

### T3 — Production

Everything in T2, plus:

```
repo/
├── .github/
│   └── workflows/
│       ├── test.yml
│       ├── pages.yml
│       └── deploy.yml    # optional until deploy target exists
├── Dockerfile            # if containerized
└── docs/                 # runbooks, API contracts (optional)
```

---

## File reference

| File | Purpose | Tier |
|------|---------|------|
| `README.md` | User-facing overview | T0+ |
| `.gitignore` | Exclude secrets, caches, artifacts | T0+ |
| `plan.md` | Implementation status and milestones | T1+ |
| `.env.example` | Documented env template | T1+ (if env vars) |
| `LICENSE` | Legal terms | T2+ public |
| `presentation.html` | Demo slide deck for GitHub Pages | T2 public repos |
| `pyproject.toml` | Python project + tool config | T1+ Python (preferred) |
| `requirements.txt` | Pip deps (pin for apps) | T1+ Python |
| `uv.lock` | Reproducible uv deps | Optional; recommended for agentic tooling |
| `CONTRIBUTING.md` | Contributor guide | Optional; T3 or open collab |
| `CHANGELOG.md` | Release history | Optional; T3 |

---

## .gitignore baseline (Python)

```gitignore
# Secrets
.env
.env.local
*.pem
*.key

# Python
__pycache__/
*.py[cod]
.venv/
venv/
.pytest_cache/
.ruff_cache/
.mypy_cache/
.coverage
htmlcov/
dist/
build/
*.egg-info/

# IDE
.idea/
.vscode/
*.swp
.DS_Store

# Project-specific (add as needed)
models/*.joblib
data/raw/
*.log
```

Add language-specific blocks for Go (`bin/`), Node (`node_modules/`), Rust (`target/`).

---

## CI workflow standard

### Python — minimum `test.yml`

```yaml
name: Test

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Lint
        run: pip install ruff && ruff check .

      - name: Test
        run: |
          pip install pytest pytest-cov
          pytest --cov=. --cov-report=term-missing --cov-fail-under=25
```

### Coverage gate tiers

| Project maturity | Minimum coverage | Lint |
|------------------|------------------|------|
| Early / spike | No gate | Optional |
| Active dev (T1) | **25%** | ruff required |
| Release-ready ML (T2) | **50%** (raise over time) | ruff required |
| Mature API / T3 production | **80%** | ruff required |

Align repo to one tier in `plan.md` and enforce in CI with `--cov-fail-under=N`.

### Actions version pins

Use current major versions consistently:

- `actions/checkout@v4`
- `actions/setup-python@v5`
- `actions/setup-go@v5`
- `actions/setup-node@v4`

---

## Dependency management

| Stack | Preferred | Also acceptable |
|-------|-----------|-----------------|
| Python (new) | `pyproject.toml` + optional `uv.lock` | `requirements.txt` with pinned versions |
| Python (Lambda) | `requirements.txt` per function | — |
| Node | `package.json` + lockfile | — |
| Go | `go.mod` | — |

**Rule:** CI must install deps the same way README documents.

---

## Tests directory

| Convention | Example |
|------------|---------|
| `tests/` (pytest default) | Python projects |
| `test/` | Some Go projects |
| Co-located `*_test.go` | Go standard |

Minimum: unit tests for business logic and API contracts at T1+.

---

## Optional but recommended

| File | When |
|------|------|
| `docker-compose.yml` | Multi-service local dev |
| `Makefile` | Common commands (`make test`, `make lint`) |
| `docs/CONTRIBUTING.md` | Open collaboration repos |
| `docs/API.md` | Large API surface |

---

## New repo bootstrap checklist

```bash
# 1. Create repo locally
mkdir my-project && cd my-project
git init

# 2. Add minimum files (copy templates from standards/)
touch README.md plan.md .gitignore .env.example LICENSE

# 3. Python setup
# pyproject.toml or requirements.txt + tests/

# 4. CI
mkdir -p .github/workflows
# copy test.yml, pages.yml templates

# 5. First commit
git add .
git commit -m "chore: initial project scaffold with project guardrails"

# 6. Publish
gh repo create my-project --public --source=. --remote=origin --push
```

---

## Compliance checklist

- [ ] Root tree matches declared tier (T0–T3)
- [ ] `.gitignore` covers `.env`, caches, artifacts
- [ ] CI runs on push/PR to main
- [ ] Coverage gate documented in plan.md and enforced in CI
- [ ] Dependencies installable from documented files only
