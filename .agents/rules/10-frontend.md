# Frontend Rules

Scope: `frontend/` and frontend/API boundary work. Evidence: R3-R7 in
[engineering findings](../../engineering-findings.md).

- Keep shared movement fields and literal values aligned with
  [financial-types.ts](../../frontend/src/lib/financial-types.ts) and backend
  response models. The existing operation value is `outcome`, not `expense`.
- Keep reusable calculations and currency/percent formatting in
  [financial-utils.ts](../../frontend/src/lib/financial-utils.ts). Preserve helper
  signatures and the zero-income guard unless the requested behavior needs a change.
- Monthly aggregation must distinguish years and stay chronological. API dates
  are ISO calendar dates, not viewer-local instants: validate first-of-month and
  year-boundary cases in UTC and a timezone west of UTC when changing date logic.
  For keys derived from parsed ISO dates, use UTC calendar getters; do not change
  a helper's parameter type when the existing Date representation suffices.
- Follow the touched file's punctuation; there is no verified uniform formatter.
  Use the existing `@/` alias for cross-directory imports and type-only imports
  for types. Keep components and shared UI primitives in their existing folders.
- Reuse [financial-utils.test.ts](../../frontend/src/lib/financial-utils.test.ts)
  for helper checks: `docker compose exec -T frontend npm test -- src/lib/financial-utils.test.ts`.
  For a helper edit, lint that file with
  `docker compose exec -T frontend npx eslint src/lib/financial-utils.ts`.
  The existing type/bundle gate is `docker compose exec -T frontend npm run build`;
  run it for type/component or build-contract changes, not documentation-only work.
- Keep the dashboard's fetch, loading, and error path in
  [App.tsx](../../frontend/src/App.tsx) intact during unrelated helper edits.
  Do not replace live API errors with mock fallback data or redesign the UI.