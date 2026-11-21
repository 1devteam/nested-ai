# Nested AI V6.6 recovery and verification status

**Scope:** repository/source orientation for the restored `main` branch. This document does not certify a deployed environment.

## Source lineage

- Project/repository: `1devteam/nested-ai` (formerly OmniPath).
- Version declared in `VERSION`: **6.6.0**.
- Historical V6.6 recovery commit: `10b80b695cbe9189f763870b7cc9ad81d103051e`.
- CI repair commit: `dbf32ef188eb32188425c71fd634d37c1a20a2fc`.
- Recovery reference: `recovery/hetzner-v6.6-staging`.
- Prior main history archive: `archive/pre-v6.6-rollback-main-2026-11-21`.

These commit references describe the recovery operation. Check the live branch head before relying on them as the latest source.

## Verified from the recovered source

- `backend/main.py` contains FastAPI application initialization.
- `backend/api/routes/missions.py` schedules mission execution using FastAPI background tasks.
- `backend/orchestration/mission_executor.py` implements the mission execution path.
- `docker-compose.staging.yml` defines PostgreSQL, Redis, NATS, Jaeger, Prometheus, Grafana, backend, and Nginx services. It does **not** define a standalone durable mission-worker service.
- `.github/workflows/tests.yml` and `.github/workflows/auto-version.yml` define automated checks and release automation.

## Verification boundaries

- CI lint, typing, and unit-test success is not equivalent to a completed real mission.
- A mission started inside the API process may be interrupted by process restart. Durable execution and recovery must be verified separately before being claimed.
- Historical test counts and coverage percentages in older documentation must be remeasured at the current commit.
- DNS records, deployment manifests, and healthy containers are separate evidence categories; none alone proves an end-to-end mission.
- Documentation of event sourcing, NATS, sagas, and governance describes implemented components or design goals, not necessarily end-to-end use in every execution path.

## Next verification sequence

1. Check the current `main` commit, version, GitHub Actions conclusions, and dependency installation.
2. Validate staging configuration with `docker compose -f docker-compose.staging.yml config` using deployment-safe environment values.
3. Bring up services in an isolated staging environment; verify health endpoints, logs, and migrations.
4. Run one read-only mission end-to-end, recording request, execution events, final state, and human-readable artifact.
5. Inject worker/API interruption and verify whether state and execution recover; record failure if not.
6. Update this document with exact run IDs, timestamps, evidence locations, and unresolved defects.

## Documentation policy

Keep historic design files available, but distinguish **implemented**, **locally tested**, **CI tested**, **runtime verified**, and **proposed**. Never turn an unverified aspiration into a production claim.
