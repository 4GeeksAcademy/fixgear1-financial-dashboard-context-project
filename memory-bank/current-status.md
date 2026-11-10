# Current Status

Last verified: 2026-11-10. This is a handover snapshot, not an ongoing health monitor.
Update this entry after behavior, setup, or verification changes; cite the changed
source and record what actually ran. Use Git history for authoritative commit IDs.

## Completed Handover

- Phase 1 (`437eb63`): inspected the two-service app; built and started Compose;
  verified health, API docs, frontend HTTP, and proxied metrics.
- Phase 2 (`8ab2b6b`): [engineering-findings.md](../engineering-findings.md)
  maps proposed rules to evidence, risks, and focused checks.
- Phase 3 (`f2877c0`): installed three scoped rules, connected them through
  [AGENTS.md](../AGENTS.md), and exercised them on a real timezone defect.
- Phase 4: this memory bank records product context, stack/operations, and status.
  Its relative links and configuration claims are checked before its own commit.
- Detailed evidence and the rejected agent proposal are in
  [verification.md](../verification.md). No new test files or product features added.

## Verified Behavior and Checks

- Compose services were still running at the final status check; `/health`
  returned `{"status":"ok"}`. Phase 1 HTTP checks passed for `/docs`, frontend `/`,
  and frontend-proxied `/api/metrics`, which returned 360 sorted records.
- [financial-utils.ts](../frontend/src/lib/financial-utils.ts) now derives month
  keys with UTC getters. One-off assertions passed in UTC and America/Los_Angeles
  for first-of-month, year boundaries, chronology, totals, and empty input.
- All 5 existing helper tests passed, scoped ESLint passed, and editor diagnostics
  reported no errors in the changed helper.
- Existing backend health test: 1 passed, 14 deselected. A Starlette TestClient
  deprecation warning says its use of httpx is deprecated in favor of httpx2.
  No dependency changes were made to suppress this inherited warning.

## Known Limits and Next Decisions

- The dashboard period label is still hardcoded to 2024; generated dates are not.
- Full frontend/backend suites, production build, and browser layout checks were
  not run. HTTP success does not establish chart rendering or accessibility.
- Backend requirements are unpinned; seeded generation still depends on today's
  date. Do not hardcode the observed dataset date range into future tests/docs.
- Reload servers, exposed debugpy, and permissive CORS are development settings;
  production hardening was not part of this task.
- The existing delivery fork is organization-owned:
  https://github.com/4GeeksAcademy/fixgear1-financial-dashboard-context-project.
  GitHub confirmed its parent and push permission. The assignment's personal-fork
  requirement may need instructor confirmation; ownership was not changed.
- Services are left running for inspection. Use the documented Compose startup
  and health checks again in a fresh environment; do not assume this snapshot
  means containers will be running for the next contributor.