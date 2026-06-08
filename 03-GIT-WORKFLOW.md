# Git Workflow Standard

**Applies to:** All git repositories in this workspace  
**Tier:** Required at **T0+**

---

## Branch strategy

| Branch | Purpose |
|--------|---------|
| `main` | Default for **new** repos; stable, deployable dev baseline |
| `master` | Legacy — keep on existing repos; do not rename casually |
| `feature/<short-description>` | New features |
| `fix/<short-description>` | Bug fixes |
| `docs/<short-description>` | Documentation-only |
| `chore/<short-description>` | Tooling, deps, CI |

**Optional prefix with ticket:** `feature/QM-42-pfmea-filter`

Rules:

- Never commit directly to `main` for shared team repos (use PRs).
- One logical change per branch.
- Delete feature branches after merge.

---

## Commit messages

Use **[Conventional Commits](https://www.conventionalcommits.org/)** across all repos in the workspace.

### Format

```
<type>(<optional scope>): <short description>

[optional body — explain why, not what]

[optional footer: Fixes #123]
```

### Types

| Type | When |
|------|------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `docs` | README, plan, comments only |
| `test` | Tests only |
| `refactor` | Code change, no behavior change |
| `chore` | Deps, CI, tooling |
| `perf` | Performance improvement |
| `ci` | CI/CD pipeline changes |

### Scopes (optional)

Use subsystem names: `feat(agents):`, `fix(api):`, `docs(readme):`

### Examples

```
feat: add optional API key auth middleware
fix(dev): evaluate.py NameErrors for macro/cv metrics
docs: align README with project guardrails
test: add SSRF guard tests for qualitymind client
feat(dev): structure_check on agent routes when ENVIRONMENT=development
```

### Rules

- Subject line ≤ 72 characters; imperative mood ("add", not "added").
- Body explains **why** when the change is non-obvious.
- Do not mix unrelated changes in one commit.
- Do not commit secrets, `.env`, large binaries, or `.coverage` artifacts.

---

## Push workflow

### Initial publish

```bash
# From repo root, after first commit
git remote add origin git@github.com:<your-username>/<repo-name>.git
git branch -M main
git push -u origin main
```

Or with GitHub CLI:

```bash
gh repo create <repo-name> --public --source=. --remote=origin --push
```

### Daily workflow

```bash
git checkout -b feature/my-change
# ... edit, test ...
git add <files>
git commit -m "feat: describe change"
git push -u origin feature/my-change
gh pr create --title "feat: describe change" --body "## Summary\n..."
```

### Before every push

1. Run local tests (`pytest`, `npm test`, etc.).
2. Confirm no secrets in diff: `git diff --staged`.
3. Confirm branch is not accidentally pushing to wrong remote.

### Force push

- **Never** force-push to `main` / `master`.
- Force-push feature branches only when rebasing your own work and no one else depends on the branch.

---

## Pull requests

### PR title

Same format as commit subject: `feat: add routing eval harness`

### PR body template

```markdown
## Summary
- Bullet 1: what changed and why
- Bullet 2

## Test plan
- [ ] `pytest` passes locally
- [ ] Manual smoke test (describe command)
- [ ] No secrets in diff

## Guardrails
- [ ] `.env.example` updated (if env vars changed)
- [ ] README updated (if behavior changed)
- [ ] plan.md status updated (if scope changed)
```

### Merge policy

- CI must pass before merge.
- Squash merge preferred for feature branches (clean history).
- One approval for shared team repos when collaborating.

---

## Tags and releases

| Tag format | Use |
|------------|-----|
| `v0.1.0` | Semver releases |
| `v0.1.0-dev.1` | Pre-release / dev milestones |

Annotate tags with release notes when publishing release milestones.

---

## Files that must never be committed

Ensure these are in `.gitignore`:

```
.env
.venv/
__pycache__/
.pytest_cache/
.ruff_cache/
.coverage
htmlcov/
node_modules/
dist/
*.pem
*.key
.DS_Store
```

See [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) for full template.

---

## Compliance checklist

- [ ] Conventional Commits used consistently
- [ ] Feature branches + PRs for shared repos
- [ ] No secrets in git history
- [ ] CI passes before merge
- [ ] PR includes test plan
