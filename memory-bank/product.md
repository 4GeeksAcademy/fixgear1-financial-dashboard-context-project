# Product Context

Verified against the linked source on 2026-11-10. Update when scope or data flow changes.

## Purpose and Current Experience

Financial Metrics Dashboard is a demonstration dashboard for generated financial
movements, not an accounting system backed by persisted business records.
[App.tsx](../frontend/src/App.tsx) fetches `/api/metrics` and displays income,
outcome, profit, profit margin, and monthly charts, with loading and error states.
[financial-utils.ts](../frontend/src/lib/financial-utils.ts) computes the displayed
KPIs and monthly values; [kpi-row.tsx](../frontend/src/components/dashboard/kpi-row.tsx)
uses shared currency and percentage formatters.

## Data and Service Boundary

- [routes.py](../backend/app/routes.py) generates 360 movements using seed 42,
  with dates relative to `date.today()`. It does not retrieve these from a database.
- Movement fields and literals are defined in the backend Pydantic model and
  [financial-types.ts](../frontend/src/lib/financial-types.ts). `outcome` is the
  existing expenditure operation; `business_type` is `B2B` or `B2C`.
- The backend also exposes B2B/B2C movement views, facets, grouped summaries,
  top categories, period comparisons, and outcome alerts under `/api/metrics`.
  Those endpoints are not currently fetched by the dashboard in App.tsx.
- Requests flow browser -> Vite `/api` proxy -> FastAPI -> generated movements
  -> frontend financial helpers -> dashboard components.

## Scope Limits

The visible period header is hardcoded to `2024 - Full Year`, while generated data
is date-dependent. Treat that mismatch as a known limitation, not a requirement
that the dataset be fixed to 2024. The inherited source does not establish a
production authentication or persistence workflow. No feature expansion or visual
redesign was needed for this handover; only a timezone grouping defect was fixed.

For verified runtime results and rejected claims, see
[verification.md](../verification.md). For current work and remaining risks, read
[current-status.md](current-status.md).