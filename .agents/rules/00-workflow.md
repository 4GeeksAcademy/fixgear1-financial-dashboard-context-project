# Workflow Rules

Scope: every repository task. Evidence: R1, R2, R7, R8 in
[engineering findings](../../engineering-findings.md).

- Start from the requested file or behavior. State a short plan for non-trivial
  work; inspect the owning code and a nearby check before editing.
- Separate code/configuration evidence, observed runtime results, and unknowns.
  Correct rejected claims in [verification.md](../../verification.md).
- Preserve unrelated work. Keep changes confined to the requested phase or task;
  do not add frameworks, refactors, or product features to improve documentation.
- Derive service commands and ports from [Compose](../../docker-compose.yml),
  Dockerfiles, and package scripts. `backend:8000` is Compose-internal; 5678 is
  debugpy, not HTTP. Do not silently repoint the shared proxy for native setup.
- Run a focused existing check immediately after a behavior change. Reuse tests;
  do not create test files unless requested. After two failed repair attempts,
  stop and report the exact error, changes tried, and remaining hypothesis.
- Do not describe mock data or reload/debug servers as production infrastructure.
  Keep secrets and generated dependencies/build outputs out of commits, following
  the existing [.gitignore](../../.gitignore).
- When behavior or setup changes, update the affected memory entry with file
  evidence and check results. Do not turn a runtime snapshot into a permanent fact.
- For this handover, validate each phase before its own commit; do not combine
  findings, rule implementation, and project memory into one commit.