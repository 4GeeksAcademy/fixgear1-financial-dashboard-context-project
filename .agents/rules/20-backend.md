# Backend Rules

Scope: `backend/` and API-contract work. Evidence: R2, R3, R5, R7, R8 in
[engineering findings](../../engineering-findings.md).

- Follow the existing router, Pydantic response models, typed query parameters,
  and helpers in [routes.py](../../backend/app/routes.py). Do not introduce a
  second app or a persistence layer for a local route change.
- For API changes inspect the matching
  [frontend types](../../frontend/src/lib/financial-types.ts) and caller.
  Preserve movement-array payloads and existing literals unless explicitly changing
  the contract; backend summary responses are not interchangeable with movements.
- Preserve inclusive date boundaries and chronological movement responses.
  Reuse filtering and sorting helpers rather than adding a parallel implementation.
- Generated data uses seed 42 and `date.today()`; assert relevant shapes, filters,
  and ordering rather than treating a particular runtime year as permanent data.
- Select relevant existing tests in [test_routes.py](../../backend/tests/test_routes.py):
  `docker compose exec -T backend python -m pytest tests/test_routes.py -k health -q`
  checks health; choose the appropriate test name for the changed route/helper.
  Run backend tests from the backend working directory (Compose uses `/app`).
- Do not change permissive CORS, debugpy exposure, dependencies, or startup commands
  as incidental cleanup. These inherited development settings are not evidence of
  authentication, production hardening, or pinned dependency versions.