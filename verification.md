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