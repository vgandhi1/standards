# Testing Standard

**Applies to:** All projects with runnable code (**T1+**)  
**See also:** [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) for coverage gates, [08-AI-SECURITY.md](08-AI-SECURITY.md) for LLM-specific test requirements

---

## Principles

1. **Tests must be fully offline.** No live API calls (OpenAI, Pinecone, PostgreSQL, S3) in the test suite. Mock everything external.
2. **Tests must be deterministic.** Same input → same result on every run, on every machine.
3. **Tests are a contract.** A test that passes despite broken behavior is worse than no test. Don't write tests that always pass.
4. **Coverage is a floor, not a goal.** Hit the gate; then write tests for the cases that would actually hurt if they broke.

---

## Test types and when to use each

| Type | When | Example |
|------|------|---------|
| **Unit** | Pure functions, business logic, data transforms | `test_classify_label()`, `test_dangerous_sql_check()` |
| **Integration** | Multiple components wired together with mocked I/O | API endpoint + service layer + mocked DB |
| **Contract** | Inter-service schema compatibility | Service A's output JSON matches Service B's expected input schema |
| **Eval / regression** | ML model accuracy, LLM output quality | RAGAS metrics against held-out eval dataset |
| **End-to-end** | Full stack, live dependencies | Deferred to T3 staging environment — not in CI |

---

## Directory structure

```
tests/
├── unit/           # Pure logic, no I/O
│   └── test_<module>.py
├── integration/    # Mocked external services
│   └── test_<feature>.py
├── contract/       # Schema and API compatibility (if cross-service)
│   └── test_handoff_schema.py
├── evals/          # ML/LLM accuracy regressions (not in default CI run)
│   └── test_ragas.py
└── conftest.py     # Shared fixtures, mock factories
```

Co-located `*_test.go` files are the Go convention — follow it for Go projects.

---

## Naming conventions

| Convention | Example |
|------------|---------|
| File: `test_<module>.py` | `test_extract.py`, `test_agent_routes.py` |
| Test function: `test_<behavior>_<condition>` | `test_classify_returns_unknown_below_threshold()` |
| Fixture: noun, singular | `mock_pinecone_client`, `sample_pfmea_record` |

Test names should read as specifications: *"when X, it should Y."* A failing test name should tell you exactly what broke without reading the body.

---

## Mocking strategy

**Rule:** Mock at the I/O boundary — not deep inside business logic.

| What to mock | How |
|-------------|-----|
| LLM API (OpenAI, Anthropic) | Return a fixed JSON fixture; never call real API |
| Vector store (Pinecone) | Return fixed chunk list; never call real index |
| Database (PostgreSQL, SQLite) | Use in-memory SQLite or fixtures; never touch dev DB |
| Outbound HTTP clients | `httpx.MockTransport` or `responses` library |
| File system | `tmp_path` fixture (pytest built-in) |

```python
# conftest.py — central mock factory pattern
@pytest.fixture
def mock_openai(monkeypatch):
    def fake_chat_completion(*args, **kwargs):
        return {"choices": [{"message": {"content": '{"label": "soft_reset"}'}}]}
    monkeypatch.setattr("app.services.llm_service.call_llm", fake_chat_completion)
```

Never use `time.sleep()` in tests. If you need to test async behavior, use `pytest-anyio` or `asyncio.run()`.

---

## Coverage gates

Coverage gates are enforced in CI via `--cov-fail-under=N`. See [06-REQUIRED-FILES.md](06-REQUIRED-FILES.md) for the full tier table.

| Tier | Gate | Note |
|------|------|------|
| T1 (active dev) | 25% | Covers happy paths |
| T2 (release-ready) | 50% | Covers error branches |
| T3 (production) | 80% | Near-complete coverage of service logic |

**Exclude from coverage:** migration files, generated code, `__init__.py` stubs, eval scripts, `presentation.html`.

```toml
# pyproject.toml
[tool.coverage.run]
omit = [
    "*/migrations/*",
    "*/evals/*",
    "tests/*",
    "*/__init__.py",
]
```

---

## ML and eval tests

Eval tests are not part of the standard CI run — they are slow, require live models or datasets, and belong in a separate job or manual step. They are required before any T2 release checkpoint.

| What to test | How | Threshold |
|-------------|-----|-----------|
| Classifier accuracy | Holdout test set, `sklearn.metrics` | Project-specific; document in `plan.md` |
| RAG faithfulness | RAGAS `faithfulness` metric | ≥ 0.75 (default) |
| RAG answer relevancy | RAGAS `answer_relevancy` metric | ≥ 0.80 (default) |
| Regression gate | Compare new metric vs stored baseline | Fail if below threshold by > 0.02 |

Store eval baselines in `evals/baselines.json` (committed). Update only when intentionally improving the model.

```json
{
  "classifier_macro_f1": 0.91,
  "ragas_faithfulness": 0.82,
  "ragas_answer_relevancy": 0.84,
  "updated": "2026-06-07"
}
```

---

## CI test job

CI job structure and workflow YAML are owned by agent-forge (`ship` preset). The rules this standard enforces on the CI test job are:

- Lint → unit tests → integration tests → coverage check must all pass before merge.
- Eval tests (`tests/evals/`) run in a separate job — never block PR merge on eval performance.
- The coverage gate enforced in CI must match the tier declared in `plan.md` (see coverage gate table above).

---

## Test data and fixtures

- **Never commit real production data** as test fixtures — use anonymized or synthetic data.
- Store static fixtures in `tests/fixtures/` as JSON or CSV files.
- Generate dynamic fixtures in `conftest.py` using factory functions.
- Model weights and large binary files must never be in the test fixtures directory. Mock the model loader instead.

```python
# tests/fixtures/sample_claim.json
{
  "claim_id": "test-001",
  "description": "Device failed to power on after firmware update",
  "part_number": "PART-TEST-001"
}
```

---

## What tests must not do

- Call live external APIs (OpenAI, Pinecone, AWS, databases) without an explicit `@pytest.mark.integration` marker and opt-in flag
- Assert on exact LLM output text — assert on schema, labels, or score ranges instead
- Use hardcoded absolute paths — use `pathlib.Path(__file__).parent` or `tmp_path` fixtures
- Sleep or use wall-clock time as a synchronization mechanism
- Modify shared state without cleanup (use fixtures with teardown)
- Depend on test execution order

---

## Compliance checklist

- [ ] Tests directory exists (`tests/` or `test/`)
- [ ] All tests pass offline — no live API calls in default test run
- [ ] Coverage gate documented in `plan.md` and enforced in CI (`--cov-fail-under=N`)
- [ ] Mocks live in `conftest.py` — not scattered inline across test files
- [ ] Test names describe behavior, not implementation
- [ ] No real production data in `tests/fixtures/`
- [ ] Eval tests isolated to `tests/evals/` — not blocking CI by default
- [ ] Eval baselines committed to `evals/baselines.json` (if ML project)
