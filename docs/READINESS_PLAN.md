# Nested AI — Readiness and Advancement Plan

**Baseline date:** 2026-11-21  
**First operational checkpoint:** 2026-11-22  
**Scope:** Restored OmniPath V6.6 / Nested AI. This is a readiness plan, not a declaration of readiness.

## 1. Mission and definition of done

Restore the existing V6.6 system to a reproducible, evidence-backed operating state before introducing major architecture changes. Then develop Nested AI's recursive verification and improvement capability (RVIS) within the existing system rather than as an unrelated replacement.

**A system is not ready because a workflow is green, a container is healthy, or a document says it is.** Readiness requires observed behavior and durable, reviewable evidence.

### Readiness levels

- **R0 — Source recovered:** verified branch ancestry, version, preservation of rollback and archive refs.
- **R1 — Build and CI:** reproducible dependency installation; formatting, lint, tests, type/security findings classified; images build.
- **R2 — Staging operation:** isolated environment starts, migrations and health probes succeed, credentials and network boundaries checked.
- **R3 — Mission operation:** a read-only mission completes through API → orchestration → agents/tools → persisted outcome → human-readable artifact, with traceable evidence.
- **R4 — Resilience:** failure/restart tests demonstrate correct terminal state, idempotency, recovery or an explicitly documented unsupported behavior.
- **R5 — Controlled production:** backups/restores, alerts, secrets, access control, rollback, and sustained operational tests verified.
- **R6 — Recursive improvement:** capabilities have dependency/evidence records; defects can be detected, reproduced, repaired, regression-tested, and evaluated against the system baseline.

Do not mark a level passed until its exit evidence exists.

## 2. Known baseline and evidence limits

- Repository: `1devteam/nested-ai`; restored source baseline `10b80b695cbe9189f763870b7cc9ad81d103051e`; CI repair `dbf32ef188eb32188425c71fd634d37c1a20a2fc`.
- Declared version: `6.6.0`; earlier OmniPath/Citadel names remain in files and deployment configuration.
- The source contains FastAPI, mission routes/executor, agent/tool integrations, PostgreSQL, Redis, NATS, observability, and staging Compose.
- `backend/api/routes/missions.py` uses FastAPI background tasks for mission execution; no standalone durable mission worker appears in `docker-compose.staging.yml`. Do **not** assume restart-safe execution.
- CI's mypy, Bandit, Safety, and integration test steps are currently configured with `continue-on-error: true`; a green job does not establish that these checks passed.
- The uploaded source archive predates the subsequent documentation commits. Verify the live branch head before each change.
- Actual Hetzner deployment state, migrations, end-to-end mission results, data durability, backup restoration, and runtime coverage remain **unverified** in this plan.

## 3. Today: 2026-11-21 — establish a trustworthy baseline

### Gate A — source and governance

- [ ] Record `git rev-parse HEAD`, `git status --short`, `cat VERSION`, and `git branch -avv`.
- [ ] Confirm `main` is the intended default branch and rulesets target it; preserve recovery/archive branches.
- [ ] Record current GitHub Actions run IDs and **step-level** conclusions; do not count allowed-to-fail steps as passing.
- [ ] Inventory deploy entry points, database migrations, authentication configuration, mission state transitions, tool egress, persistence, and observability.
- [ ] Classify findings: **verified runtime**, **verified source issue**, **evidence artifact**, **unverified hypothesis**.
- [ ] Freeze unrelated feature development until R1–R3 gates are understood.

**Exit evidence:** commit/branch record, CI report, dependency inventory, and an issue register with source paths and reproduction instructions.

### Gate B — build and test truth

- [ ] Recreate Python 3.11 environment and install `requirements.txt` without manual undocumented fixes.
- [ ] Run Black, Flake8, unit tests, integration tests with prerequisites, mypy, Bandit, Safety, and Docker builds.
- [ ] Capture full command, environment, exit code, failures, skips, xfails, and coverage report.
- [ ] Separate unavailable services from actual application failures.
- [ ] Replace swallowed test failures with explicit failure reporting **after** baseline findings are understood; do not merely remove `continue-on-error` and declare success.
- [ ] Create regression tests for every repaired failure.

**Exit evidence:** reproducible test report; known blockers assigned; no unexamined red/allowed-to-fail checks.

## 4. Tomorrow: 2026-11-22 — first operational proof

The date is a checkpoint, **not a promise of completion**. Stop at a failing gate rather than bypassing it.

### Morning — staging boot and safety

1. Confirm branch SHA and artifact versions; review the deployment environment without exposing secrets.
2. Back up existing database and volumes before migration or replacement; record backup location and test restore procedure.
3. Validate `docker compose -f docker-compose.staging.yml config` and all required variables, TLS mounts, image tags, ports, and service dependencies.
4. Start an **isolated staging environment**; verify PostgreSQL, Redis, NATS, backend, proxy, and observability health.
5. Check database migrations/schema compatibility, authentication, authorization, and logs. No destructive reset of existing data.

**Go criterion:** services healthy **and** API, persistence, and auth smoke tests succeed. Otherwise **NO-GO** for mission proof; repair the identified blocker.

### Midday — one controlled mission

Use a safe **read-only** mission with a small, deterministic target and permitted sources. No CRM writes, outreach, purchases, or external mutations.

1. Submit via the actual supported API/UI route.
2. Record request ID, mission ID, selected plan, agent/tool invocation, source provenance, and timestamps.
3. Verify each transition against persisted state, not only log text.
4. Verify that retrieved facts correspond to actual sources; distinguish search candidates from source-verified entities.
5. Verify a terminal success or explicit failure with a human-readable artifact; check content, structure, completeness, and readback after restart.
6. Reconcile API response, database record, events, logs, and artifact. Investigate any disagreement as a system defect.

**Go criterion:** one complete, source-grounded, read-only mission with consistent persisted evidence and a retrievable artifact. A successful HTTP response alone is insufficient.

### Afternoon — failure and recovery proof

- Inject controlled API restart during a mission, tool timeout, missing source, database/NATS interruption, and duplicate request **one at a time**, with recovery of the environment between experiments.
- Record whether missions resume, fail explicitly, become stuck, duplicate effects, or lose artifacts.
- Verify no silent success, invented source, repeated unintended execution, or ambiguous terminal state.
- Identify the minimum coherent architecture repair for mission durability; do not add a separate worker merely to satisfy a diagram.

**Go criterion:** every injected failure has a measured outcome, safe behavior, and either verified recovery or a documented blocking defect.

### End-of-day deliverables

- A signed-off **GO / CONDITIONAL GO / NO-GO** decision per readiness level.
- Evidence bundle: commit SHA, CI links, Compose config hash, container status, sanitized logs, mission IDs, database/event snapshots, artifact, and failure-injection results.
- Prioritized blockers with owner, root-cause evidence, affected components, tests required, and proposed repair boundary.
- A short next-day execution queue based on observed failures, not speculative features.

## 5. Following workdays — dependency-ordered advancement

### Phase 1 — close operational gaps (R2–R4)

- Fix the **complete mission lifecycle**: accepted → planned → executing → terminal outcome; durable state, idempotent execution, explicit retries and timeouts, recovery on process restart.
- Align API, orchestrator, tools, persistence, event store, monitoring, and artifact generation around one canonical mission identity and outcome definition.
- Add end-to-end and fault-injection regression tests for each failure mode.
- Preserve functioning V6.6 behavior; prove no regressions before merge.

**Exit:** repeatable missions with persisted outcomes, deterministic failure semantics, and verified restart behavior.

### Phase 2 — production controls (R5)

- Validate least-privilege credentials, secret rotation, outbound tool permissions, rate limits, and audit trails.
- Prove backup **and restore**; migrations forward/backward or a documented safe rollback path.
- Add actionable alerting, runbooks, SLOs, and recovery-time objectives based on observed load.
- Test cold deployment and rollback on a clean host before production promotion.
- Run a defined soak/load test with measurable error rate, latency, resource use, and data integrity.

**Exit:** operational approval supported by security, resilience, observability, and rollback evidence.

### Phase 3 — RVIS foundation (R6, part A)

- Inventory existing capabilities and map each to source entry points, dependencies, tests, runtime evidence, failure signals, and responsible components.
- Establish a versioned capability/evidence registry and cross-component consistency checks.
- Instrument mission and subsystem execution so gaps can be detected automatically.
- Report coverage by **capability and supported path** as well as code line/branch; never equate 100% coverage with correctness.

**Exit:** a machine-readable baseline that identifies what is verified, what is broken, and what is untested.

### Phase 4 — recursive diagnosis and improvement (R6, part B)

- Observe → correlate → diagnose → formulate repair → generate regression proof → verify in isolation → system-wide regression → integrate → retain evidence.
- First proof: inject a known defect; detect it; locate its source; create a test that fails before repair and passes after; demonstrate no collateral regression.
- Keep proposed changes isolated until governance and verification permit integration. Do not allow unreviewed autonomous production edits.
- Record failed hypotheses and repair attempts so the system does not repeat them blindly.

**Exit:** one fully evidenced improvement cycle; then extend to multiple capabilities and fault classes.

## 6. Release and change policy

- Prefer coherent changesets that repair an entire affected execution path rather than many disconnected patches.
- Before modifying code: read relevant files fully, search all callers, trace data/state flows, and enumerate affected tests.
- Each changeset requires tests, rollback notes, documentation, and an evidence comparison against the prior baseline.
- Keep release versioning aligned with reachable release history; do not generate an automatic major release from historical tags after rollback.
- Do not deploy merely because a pull request merged. Deployment is a separate verified gate.

## 7. Readiness scorecard template

For each gate record:

`Gate | Status (PASS / FAIL / BLOCKED / UNVERIFIED) | Source SHA | Evidence link | Owner | Next action | Verified at`

**Decision rule:** Unknown evidence is **UNVERIFIED**, not PASS. A critical security, persistence, or mission-integrity defect blocks promotion. Noncritical defects may allow **CONDITIONAL GO** only with explicit scope and acceptance criteria.

## 8. Immediate next actions

1. Confirm latest `main` and complete CI conclusions, including checks marked `continue-on-error`.
2. Capture staging prerequisites and deployment state; do not infer Hetzner health from source.
3. Execute a clean staging boot and one read-only mission.
4. Run controlled restart and duplicate-execution experiments.
5. Publish the evidence bundle and promote only verified capabilities.

**Target outcome for 2026-11-22:** a truthful, inspectable readiness decision and one complete operational mission proof if prerequisites pass. **No-GO with precise evidence is a valid and useful result.**
