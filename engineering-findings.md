# Engineering Findings and Proposed Rules

Phase 2, inspected against the inherited source at `437eb63`.
These are proposed task instructions, not claims that rule enforcement already exists.

| ID | Observed convention and evidence | Risk and proposed instruction | Focused check |
| --- | --- | --- | --- |
| R1 | [AGENTS.md](AGENTS.md) requires reading rules, skills, and memory before work. Phase 1 found those directories absent. | Explicitly load relevant rule contents through AGENTS; do not assume arbitrary Markdown under `.agents/rules` is automatically injected by every editor. Record evidence and unknowns before editing. | Inspect AGENTS discovery instructions and exercise them in Phase 3. |
| R2 | [docker-compose.yml](docker-compose.yml), both Dockerfiles, and [frontend/vite.config.ts](frontend/vite.config.ts) define the two-service development topology. | Keep the `/api` proxy contract and documented ports aligned. `backend` is a Compose hostname, not a general native-development hostname. Port 5678 is debugpy. | Compose config; backend health; frontend-proxied metrics request. |
| R3 | [backend/app/routes.py](backend/app/routes.py) and [frontend/src/lib/financial-types.ts](frontend/src/lib/financial-types.ts) share `create_date`, `amount`, `operation_type`, `category`, and `business_type`; literals include `outcome`, not `expense`. | For contract edits inspect both representations and their callers. Do not silently rename fields, enum values, or replace movement arrays with summary objects. | Relevant route test and frontend type-check for a contract edit. |
| R4 | [frontend/src/App.tsx](frontend/src/App.tsx) fetches movements and delegates calculations to [financial-utils.ts](frontend/src/lib/financial-utils.ts); [kpi-row.tsx](frontend/src/components/dashboard/kpi-row.tsx) delegates formatting there too. | Keep reusable financial calculation and formatting behavior in the existing helpers, not duplicated in components or replaced with unrelated backend analytics. Preserve signatures unless the task needs a contract change. | Existing helper tests, then lint the touched helper. |
| R5 | [financial-utils.test.ts](frontend/src/lib/financial-utils.test.ts) checks cross-year ordering, zero-income margin, USD rounding, and percent precision. [backend/tests/test_routes.py](backend/tests/test_routes.py) checks date boundaries and filters. | Preserve chronology, year-aware grouping, inclusive date filters, and zero-income handling. Date-only API values deserve timezone checks: `new Date(create_date)` plus local getters in `toYearMonthKey` may assign the previous month west of UTC. This risk is not yet reproduced. | Run existing helper tests and a date-boundary check in UTC and America/Los_Angeles. |
| R6 | [frontend/tsconfig.app.json](frontend/tsconfig.app.json) enables unused-code checks and the `@/*` alias; [frontend/eslint.config.js](frontend/eslint.config.js) configures TypeScript, React Hooks, and React Refresh rules. Source files use mixed punctuation styles. | Use the existing alias for cross-directory frontend imports, type-only imports for types, and neighboring file style. Do not invent a mandatory formatter, blanket strict-mode claim, or restyle unrelated files. | Targeted ESLint; existing build script when type or component changes warrant it. |
| R7 | [frontend/package.json](frontend/package.json) defines Vitest, ESLint, and build scripts; [backend/requirements.txt](backend/requirements.txt) includes pytest; [conftest.py](backend/tests/conftest.py) configures the backend import path. | Reuse existing checks. Run pytest from the backend working directory or select the existing test path. Do not add a competing test framework or test files without a request. Stop and report after two failed repair attempts. | `docker compose exec -T frontend npm test -- src/lib/financial-utils.test.ts`; select backend tests with `python -m pytest tests/test_routes.py -k ...`. |
| R8 | [backend/app/routes.py](backend/app/routes.py) generates seeded records using `date.today()`; [App.tsx](frontend/src/App.tsx) has a static 2024 header. [backend/app/main.py](backend/app/main.py) enables wildcard CORS and the Dockerfiles start development servers. | Do not describe this as persisted production data, fixed-year data, authenticated access, or a production deployment. Record limitations rather than expanding the product to remove them. Do not change CORS or debugger exposure during unrelated work. | Trace the data source and label claims as code-verified, runtime-verified, or unverified. |

## Proposed Rule Exercise

Small real task: reproduce and, only if confirmed, correct date-only monthly grouping
west of UTC without changing helper signatures, UI layout, API fields, or formatting.
Apply R4-R7: edit the existing helper only, reuse its existing tests, and run
explicit UTC and America/Los_Angeles boundary assertions without adding test files.
Record before/after results and the rules actually followed in the verification trail.

## Boundaries

- No application source changes, rule installation, or memory-bank creation in Phase 2.
- No new framework, formatter, persistence layer, visual redesign, or endpoint proposed.
- The existing backend dependency list is unpinned; do not claim reproducible Python
  dependency versions from the requirements file alone.
- Rule instructions guide an agent; they are not deterministic hooks or CI enforcement.