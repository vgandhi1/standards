# Open Source License Standard

**Applies to:** All **public** GitHub repos; recommended for all release-ready (T2+) projects  
**Allowed licenses:** **MIT** or **Apache-2.0** — pick **exactly one** per repository

---

## One license per repo

| Rule | Detail |
|------|--------|
| **Pick one** | Each repo has a single license: **MIT** (default) **or** **Apache-2.0** |
| **Never dual-license** | Do not ship both MIT and Apache files, or mix license text in README vs `LICENSE` |
| **Sync everywhere** | Root `LICENSE`, README License section, and `pyproject.toml` / `package.json` must agree |

### When to choose which

| License | Choose when |
|---------|-------------|
| **MIT** (default) | General open source, libraries, demos, ML projects — simple permissive terms |
| **Apache-2.0** | Production services where **explicit patent grant** matters; enterprise-facing APIs; Apache ecosystem alignment |

If unsure, use **MIT**.

Other licenses (GPL, AGPL, proprietary) require explicit human approval — not covered by this standard.

---

## When a LICENSE file is required

| Repo visibility | LICENSE file | Notes |
|-----------------|:------------:|-------|
| Public on GitHub | **Required** | GitHub uses root `LICENSE` for license detection |
| Private, may go public later | **Recommended** | Add before first public push |
| Private, never public | Optional | Still good practice for clarity |
| Fork of upstream | **Keep upstream license** | Do not relicense without permission |

**Do not** declare a license only in `pyproject.toml` or README without a root `LICENSE` file — both are required for public repos.

---

## MIT License (default)

1. Create `LICENSE` at repo root (exact filename, no extension).
2. Use standard MIT text (replace `{YEAR}` and `{COPYRIGHT HOLDER}`):

```
MIT License

Copyright (c) {YEAR} {COPYRIGHT HOLDER}

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

**Copyright holder:** `Vinay Gandhi` — stay consistent across repos.

### Sync metadata (MIT)

**`pyproject.toml`:**

```toml
[project]
license = { text = "MIT" }
```

**README.md:**

```markdown
## License

MIT — see [LICENSE](LICENSE).
```

---

## Apache License 2.0

Use when patent protection or enterprise compliance requires Apache-2.0.

1. Create `LICENSE` at repo root with full [Apache-2.0 text](https://www.apache.org/licenses/LICENSE-2.0.txt).
2. Add `NOTICE` file if required by bundled Apache-licensed components.

### Sync metadata (Apache-2.0)

**`pyproject.toml`:**

```toml
[project]
license = { text = "Apache-2.0" }
```

**README.md:**

```markdown
## License

Apache-2.0 — see [LICENSE](LICENSE).
```

---

## Third-party and model weights

- **Code** you wrote → your chosen license (MIT or Apache-2.0).
- **Vendored code** → keep original license files in `third_party/` or attribute in README.
- **ML weights / datasets** → document license separately in README (may differ from code license).

---

## Compliance checklist

- [ ] Root `LICENSE` file exists (public repos)
- [ ] Exactly **one** license type (MIT **or** Apache-2.0)
- [ ] Copyright year is current or range (`2024-2026`)
- [ ] README License section links to `LICENSE`
- [ ] `pyproject.toml` / `package.json` license field matches
- [ ] No conflicting "All rights reserved" in README
