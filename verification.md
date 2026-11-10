# Repository Verification Trail

## Phase 1: Handover

Reviewed the inherited code before drafting rules or memory. Status labels:
VERIFIED = checked against the named files; WRONG = rejected interpretation;
UNVERIFIED = not established by inspection or execution.
The WRONG rows are hypotheses checked and rejected during this investigation,
not quotations of earlier agent answers.

| Claim | Status | Evidence / correction |
| --- | --- | --- |
| Two application services run through Docker Compose. | VERIFIED | `docker-compose.yml`: `frontend` and `backend`; no database service. |
| Frontend is React 19 + TypeScript, built with Vite 8. | VERIFIED | Dependencies and scripts in `frontend/package.json`; dashboard implementation in `frontend/src/App.tsx`. |
| Backend is FastAPI with Pydantic response models. | VERIFIED | `backend/app/main.py`, `backend/app/routes.py`. |
| Dashboard displays production financial records from a database. | WRONG | `generate_mock_movements(seed=42)` in `backend/app/routes.py` generates 360 records per request. No persistence is implemented in that path. |
| The UI fetches every analytics endpoint. | WRONG | `frontend/src/App.tsx` fetches only `/api/metrics`, then calls `computeKPIs` and `computeMonthlyData`. |
| Data is fixed to calendar year 2024. | WRONG | The header in `frontend/src/App.tsx` is hardcoded; `_year_for_month` in `backend/app/routes.py` selects years relative to `date.today()`. The current month belongs to the previous year. |
| Local URLs are frontend http://localhost:5173, API http://localhost:8000, docs http://localhost:8000/docs. | VERIFIED | Port mappings in `docker-compose.yml`; commands in both Dockerfiles; HTTP checks passed below. |
| Port 5678 is another HTTP API. | WRONG | `backend/Dockerfile` binds debugpy there. It is a debugger, not an HTTP endpoint. |
| Native Vite can resolve the default backend hostname without configuration. | WRONG | `frontend/vite.config.ts` targets `http://backend:8000`, a Compose service name. Native execution needs a resolvable backend or a `VITE_API_BASE_URL` override. |
| Authentication, persistence, deployment readiness, and browser layout are established. | UNVERIFIED | Not demonstrated by the startup checks; do not infer production readiness from a healthy development server. |

### Reproducible Startup

From the repository root:

```bash
docker compose config --quiet
docker compose up --build -d
docker compose ps
curl --fail http://localhost:8000/health
curl --fail http://localhost:8000/docs
curl --fail http://localhost:5173/
curl --fail http://localhost:5173/api/metrics
```

Expected health response: `{"status":"ok"}` (`backend/app/routes.py`).
The frontend's same-origin `/api` requests are forwarded by Vite to the
Compose backend. The backend container also exposes debugpy on port 5678.
These are development settings, including reload and source bind mounts.
Use `docker compose down` to stop the services.

### Execution Record

- Initial worktree: clean, branch `main`, starting commit `954f812`.
- `origin`: `https://github.com/4GeeksAcademy/fixgear1-financial-dashboard-context-project`.
- `upstream`: `https://github.com/4GeeksAcademy/ai-eng-financial-dashboard-context-project.git`.
- Required discovery: `.agents/rules`, `.agents/skills`, and `memory-bank` did not exist.
- `rg` was unavailable; discovery used tracked files and standard shell tools.
- `docker compose config --quiet`: passed. Docker daemon responded.
- `docker compose up --build -d`: built and started both services successfully.
- `docker compose ps`: both services running; published ports match Compose.
- `GET http://localhost:8000/health`: HTTP 200, `{"status":"ok"}`.
- `GET http://localhost:8000/docs`: HTTP 200.
- `GET http://localhost:5173/`: HTTP 200; browser rendering remains unverified.
- `GET http://localhost:5173/api/metrics`: JSON parsed and assertions passed
	for 360 records in chronological order. Observed dates: 2025-11-01 through
	2026-10-28. This is a runtime snapshot, not a fixed fixture date range.
- GitHub repository metadata: `fork=true`, parent
	`4GeeksAcademy/ai-eng-financial-dashboard-context-project`, `permissions.push=true`.
	The existing fork is organization-owned, not under the authenticated user's
	personal namespace. It has not been moved or replaced; confirm instructor
	acceptance if personal ownership is required.
- Services left running for inspection. No application source changes in Phase 1.
- Remaining phases: derive engineering findings; implement and exercise rules;
	create verified product, stack, and current-status memory.

## Phase 2: Engineering Findings

- Inspected shared financial types, helper calculations and existing tests,
	TypeScript and ESLint configuration, a KPI caller, and the remaining analytics
	route implementations. Earlier Phase 1 evidence was reused, not assumed anew.
- [engineering-findings.md](engineering-findings.md) maps eight proposed rules
	to concrete code/configuration and a focused way to validate each.
- Rejected assumption: a uniform formatter or mandatory punctuation style exists.
	Correction: the inspected helper and KPI row use different punctuation styles;
	follow the touched file instead of reformatting the repository.
- Candidate defect, not yet confirmed: ISO date-only parsing plus local calendar
	getters may shift monthly grouping west of UTC. Phase 3 will reproduce it before
	making the smallest applicable source change.
- Validation gate: local Markdown evidence links must resolve and `git diff --check`
	must pass before this phase is committed. No application behavior changed.

## Phase 3: Installed Rules and Real Task

### Discovery and Iteration

- Installed [workflow](.agents/rules/00-workflow.md),
	[frontend](.agents/rules/10-frontend.md), and
	[backend](.agents/rules/20-backend.md) rules, mapped to Phase 2 findings.
- Updated [AGENTS.md](AGENTS.md) to explicitly require loading relevant rules.
	Arbitrary rule Markdown is not claimed to be an automatic editor hook.
- Checked all rule evidence links and all three AGENTS rule references: passed.
- A read-only Explore agent investigated the date task from the root guidance.
	It reported loading all three rules and correctly reported absent project
	skills and memory. It found `toYearMonthKey` and excluded UI, route, framework,
	and persistence changes.
- Wrong agent claim: replacing the Date parameter with a string parameter and
	slicing the ISO value was described as preserving signatures. Correction:
	although exported signatures would remain unchanged, the internal signature
	would change unnecessarily. Rejected that proposal; refined the frontend rule
	to use UTC calendar getters without changing the existing Date parameter.

### Exercise Results

Task: keep ISO date-only movement values in their intended calendar month,
including first-of-month and year-boundary cases west of UTC.

- Before: the actual helper returned `Feb 2024` for `2024-02-01` under UTC,
	but `Jan 2024` under `America/Los_Angeles`: defect reproduced.
- Changed only the key expression in
	[financial-utils.ts](frontend/src/lib/financial-utils.ts):
	`getFullYear` / `getMonth` became `getUTCFullYear` / `getUTCMonth`.
	No signature, label formatter, API, component, or test-file changes.
- After: assertions passed in UTC and America/Los_Angeles for `2024-02-01`,
	unsorted `2024-12-31`, `2025-01-01`, `2025-02-01`, totals, and empty input.
	The one-off check transpiled the actual TypeScript helper with the installed
	TypeScript API and imported that output; it did not reimplement aggregation.
- `docker compose exec -T frontend npm test -- src/lib/financial-utils.test.ts`:
	all 5 existing tests passed.
- `docker compose exec -T frontend npx eslint src/lib/financial-utils.ts`: passed.
- Editor diagnostics for the touched helper: no errors. `git diff --check`: passed.
- Rules followed in the final implementation: owning helper reused (R4),
	timezone/chronology behavior checked (R5), neighboring style preserved (R6),
	focused existing checks used with no new test files (R7).
- Not run: full suites, frontend production build, or browser visual checks.
	This task changed neither public types nor UI layout. The rule exercise
	demonstrates guidance in this session, not guaranteed compliance by every agent.