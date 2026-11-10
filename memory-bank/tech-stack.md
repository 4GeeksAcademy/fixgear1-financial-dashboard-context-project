# Stack and Operations

Verified against source/configuration on 2026-11-10. Versions below are declared
dependency majors or Docker image tags, not a claim that every dependency is pinned.

## Stack and File Map

| Area | Verified configuration / responsibility |
| --- | --- |
| Browser entry | [main.tsx](../frontend/src/main.tsx): React root and StrictMode; [App.tsx](../frontend/src/App.tsx): fetch and dashboard composition. |
| Frontend tooling | [package.json](../frontend/package.json): React 19, TypeScript 6, Vite 8, Vitest 4, ESLint 9. [frontend Dockerfile](../frontend/Dockerfile): Node 24 Alpine. |
| Presentation | [index.css](../frontend/src/index.css): Tailwind CSS 4 import and theme tokens; [income-outcome-chart.tsx](../frontend/src/components/dashboard/income-outcome-chart.tsx): Recharts. Lucide icons are used in [kpi-row.tsx](../frontend/src/components/dashboard/kpi-row.tsx). |
| UI configuration | [components.json](../frontend/components.json): shadcn UI-generation style, CSS location, and aliases; not a runtime service or package script. |
| Module resolution | [vite.config.ts](../frontend/vite.config.ts) and [tsconfig.app.json](../frontend/tsconfig.app.json) configure `@/` for `src/`. Preserve both for alias changes. |
| API | [main.py](../backend/app/main.py): FastAPI app, CORS, router registration. [routes.py](../backend/app/routes.py): models, generation, filters, analytics, GET routes. |
| Backend tooling | [backend Dockerfile](../backend/Dockerfile): Python 3.13 slim, Uvicorn reload and debugpy. [requirements.txt](../backend/requirements.txt): unpinned FastAPI, Uvicorn, debugpy, pytest, pytest-cov, httpx. |
| Tests | [financial-utils.test.ts](../frontend/src/lib/financial-utils.test.ts): Vitest helper tests. [test_routes.py](../backend/tests/test_routes.py): pytest/TestClient; [conftest.py](../backend/tests/conftest.py) configures imports. |

## Supported Development Startup

Run from the repository root with Docker and Docker Compose available:

```bash
docker compose config --quiet
docker compose up --build -d
docker compose ps
curl --fail http://localhost:8000/health
curl --fail http://localhost:5173/api/metrics
```

[Compose](../docker-compose.yml) and the Dockerfile commands establish:
frontend http://localhost:5173, API http://localhost:8000,
API docs http://localhost:8000/docs, debugpy on TCP 5678 (not HTTP).
Stop with `docker compose down`. These commands were exercised in Phase 1.

The frontend defaults to same-origin `/api`; Vite proxies it to the Compose
hostname `backend:8000`. Native execution cannot assume that hostname resolves.
[frontend/.env.example](../frontend/.env.example) documents the optional
`VITE_API_BASE_URL` override consumed by App.tsx. `/health` is not covered by the
`/api` proxy; check it directly on the backend. Do not silently change the shared
proxy to accommodate a native-only environment.

## Focused Checks

```bash
docker compose exec -T frontend npm test -- src/lib/financial-utils.test.ts
docker compose exec -T frontend npx eslint src/lib/financial-utils.ts
docker compose exec -T backend python -m pytest tests/test_routes.py -k health -q
```

Select the tests relevant to the changed behavior. The frontend build script is
`npm run build` (`tsc -b && vite build`), and the whole-project lint script is
`npm run lint`; neither is claimed to have been run during this handover.
Follow the [workflow rules](../.agents/rules/00-workflow.md) and relevant scoped
rules before editing. No project skills are installed under `.agents/skills`.