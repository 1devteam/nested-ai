# Nested AI readiness evidence — 2026-11-21

**Assessment type:** source inspection plus GitHub Actions logs. **No live Hetzner or staging access was exercised.** This is not an operational sign-off.

## Evidence sources

- Source: recovered `nested-ai-main.zip` supplied for inspection, corresponding to the V6.6 recovered application source; documentation may have been updated on live `main` since archive creation.
- GitHub Actions CI run: https://github.com/1devteam/nested-ai/actions/runs/44893437620
- Source lineage: `10b80b695cbe9189f763870b7cc9ad81d103051e` recovery; `dbf32ef188eb32188425c71fd634d37c1a20a2fc` CI fixes.
- Current branch must be rechecked before repairs; do not assume these run logs describe later commits.

## Level 1 — repository readiness: PARTIAL

**Verified positive evidence**
- CI workflow jobs completed with green GitHub conclusions: lint/format, type-check job, security job, unit-test job, Docker build.
- Unit-test log: **543 passed, 369 deselected, 26 warnings**.
- Docker image build job completed successfully in the cited run.

**Verified issues that invalidate a blanket CI PASS**
1. `.github/workflows/tests.yml` type-check step uses `continue-on-error: true` and pipes `mypy ... 2>&1 | tail -30` without `pipefail`. Log reports `backend/api/routes/registry.py: error: Duplicate module named "registry" (also at "backend/agents/registry/__init__.py")`. Resolve package/module discovery and then re-run mypy with its real exit code.
2. Security job also pipes through `tail` and allows failures. Bandit reports **5 medium** and **14 low** findings (0 high) across the backend. The log excerpt includes a hardcoded `/tmp` filesystem MCP root in `backend/integrations/mcp/server_registry.py:118` and `host="0.0.0.0"` in `backend/main.py:602`. Run full machine-readable output to inventory **all five** medium findings; classify exploitability and fix or justify each before enforcing the gate.
3. Safety command `safety scan --output text` is rejected by the installed CLI: `text` is not an accepted output value. Repair invocation, verify scan actually executes, and fail on the agreed severity policy.
4. Integration test step has `continue-on-error: true` and `timeout-minutes: 5`. Only two tests were observed passing before the step timed out; **no completed integration-suite result** was established.
5. Codecov upload reports `Token required - not valid tokenless upload`; the workflow explicitly tolerates upload failure. Configure permitted upload credentials or clearly mark coverage publication unavailable.
6. The test summary reports unit results only; it must not be interpreted as an integration-suite report.

**Level 1 exit gate**
- Dependency install and image build repeatably succeed.
- Unit tests and the complete integration suite run with real, enforced exit codes.
- Mypy, Bandit, and vulnerability scans produce inspectable complete results; blockers resolved or explicitly dispositioned.
- Coverage artifacts and test summaries distinguish unit/integration and fail/unavailable states.

## Level 2 — operational readiness: UNVERIFIED

**Source-backed observations**
- `docker-compose.staging.yml` declares PostgreSQL, Redis, NATS, Jaeger, Prometheus, Grafana, backend, and Nginx.
- `backend/api/routes/missions.py` creates a persisted Mission row and schedules execution through FastAPI `BackgroundTasks`. The executor is obtained inside the background task; a failure obtaining the executor is logged and returns without an explicit mission terminal-state update in the inspected path.
- `backend/orchestration/mission_executor.py` maintains in-memory `active_missions`, uses a status callback, and treats event-store append failures as nonfatal warnings in the inspected path. This requires end-to-end consistency and restart tests.
- No standalone durable mission worker is declared in staging Compose.

**Not verified**
- Current Hetzner deployment state, real health, credentials, migration state, database durability, tenant isolation in a running deployment, real mission artifact, external source provenance, backup restoration, restart recovery, or rollback.

**Level 2 exit gate**
1. Inspect staging host state and back up volumes/database before changing them.
2. Validate Compose and secrets handling; boot isolated staging; check migrations, health, auth, and network exposure.
3. Run a read-only mission; reconcile request, execution events, persisted status, tool/source evidence, final artifact and readback.
4. Inject API restart, source failure, duplicate request, and timeout; prove safe terminal behavior or record blockers.
5. Produce sanitized evidence bundle and GO/NO-GO report.

## Level 3 — exact prerequisite work

**A. Execution truth**
- Canonical mission identity, state machine, and terminal outcome; reconcile API, executor, database, event store, and artifacts.
- Durable dispatch/claim/retry/recovery design **only after** reproducing restart behavior. Ensure idempotency and explicit error transitions.

**B. Capability-to-evidence mapping**
- Inventory capabilities and source entry points; map callers, dependencies, test cases, runtime probes, and failure signals.
- Record `implemented`, `unit-tested`, `integration-tested`, `runtime-verified`, and `unverified` separately.

**C. Automated diagnosis**
- Correlation IDs across mission events, tool calls, source retrieval, and artifacts.
- Drift checks for conflicting outcomes and lost/stale events; source provenance verification; reproducible fault injection.

**D. Safe recursive improvement**
- Observe → reproduce → locate → propose → regression-test → isolated verification → full-system comparison → governed integration → evidence retention.
- Demonstrate one seeded defect detected and repaired without regressing an established baseline before increasing autonomy.

## Immediate priority

**P0:** Repair false-green CI and complete integration suite; do not claim R1 passed yet.  
**P0:** Establish a real staging mission proof and restart behavior before R2 promotion.  
**P1:** Implement the evidence registry and cross-component consistency probes for R3 after operational truth exists.

**Decision:** R1 = PARTIAL; R2 = UNVERIFIED; R3 = NOT ESTABLISHED. No production GO is warranted from the evidence collected here.

## Follow-up: CI verification repair (2026-11-21)

Commit `d99afc0fc04c562fa4a8704e15bc2f5387a311c3` updates `.github/workflows/tests.yml` to remove permissive exit handling from mypy, Bandit, Safety, and integration tests. It also uses the supported Safety output format, raises the integration timeout to twenty minutes, and uploads coverage as a GitHub Actions artifact rather than relying on a tokenless Codecov upload.

**Status: SOURCE CHANGE APPLIED, NEW CI RESULT NOT YET VERIFIED.** The next workflow must be inspected for real mypy errors, the complete Bandit inventory, Safety findings, and integration test failures. A newly red CI is expected if previously concealed defects remain; do not revert these gates merely to regain green status.

**Outstanding:** Docker/Trivy currently permits vulnerability findings without failing its job. Review and enforce an explicit severity and exception policy after collecting full scan evidence. Runtime staging and mission proofs remain unverified.
