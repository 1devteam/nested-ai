# Nested AI

**Restored OmniPath V6.6 codebase · 1DevTeam**

Nested AI is the current repository name for the OmniPath multi-agent platform. The recovered codebase reports **version 6.6.0** in `VERSION`. Earlier names such as OmniPath and Citadel remain in source identifiers, deployment resources, and historical documents; those names do not imply separate running systems.

## What is in the repository

- A FastAPI backend with agent, mission, governance, economy, scheduling, and integration modules.
- PostgreSQL models, Redis integration, NATS messaging, event-sourcing/CQRS components, and saga orchestration.
- Docker Compose configurations for staging and production, Kubernetes manifests, and monitoring configuration.
- Tests and GitHub Actions workflows for CI and automatic versioning.

**Implementation is not deployment proof.** The presence of a module, manifest, or test does not establish that a live service or end-to-end mission has been verified. In particular, the V6.6 mission API schedules execution using FastAPI background tasks; it is not documented here as a durable, independently recoverable mission worker.

## Recovery baseline

The restored V6.6 source commit is `10b80b695cbe9189f763870b7cc9ad81d103051e`. CI repairs were applied in `dbf32ef188eb32188425c71fd634d37c1a20a2fc`. The original recovered state remains on `recovery/hetzner-v6.6-staging`; the pre-rollback history is retained on `archive/pre-v6.6-rollback-main-2026-11-21`.

See [recovery and verification status](docs/RECOVERY_STATUS.md) before relying on historical claims. Follow the [full readiness plan](docs/READINESS_PLAN.md) for the November 21–22 operational gates and subsequent development phases.

## Start locally

Prerequisites: Git, Docker with the Compose plugin, and deployment credentials.

```bash
git clone https://github.com/1devteam/nested-ai.git
cd nested-ai
cp .env.example .env
```

Set strong, unique secrets in `.env` and review the selected Compose file before starting services. **Do not deploy the development defaults unchanged.** The staging configuration requires additional credentials (including database, Redis, NATS, Grafana, and application secrets) and assumes an externally provisioned TLS/Nginx environment.

```bash
docker compose -f docker-compose.staging.yml config
docker compose -f docker-compose.staging.yml up --build -d
docker compose -f docker-compose.staging.yml ps
```

This is a configuration and launch procedure, **not** a claim that the stack has been successfully deployed or smoke-tested. See [deployment runbook](docs/ops_runbooks/01-deployment.md) for the historical procedure and [recovery status](docs/RECOVERY_STATUS.md) for current cautions.

## Development and testing

Use a Python 3.11 environment with the dependencies in `requirements.txt`.

```bash
python -m pip install -r requirements.txt
./run_tests.sh --unit-only
./run_tests.sh --integration-only
```

Integration tests may require running services and credentials. See [testing guide](docs/TESTING_GUIDE.md). The GitHub workflows are `.github/workflows/tests.yml` and `.github/workflows/auto-version.yml`. Do not interpret a successful lint check as end-to-end runtime verification.

## Documentation

Start with the [documentation index](docs/README.md), including the [architecture history](docs/ARCHITECTURE.md), [developer guide](docs/developer_guide/README.md), [user guide](docs/user_guide/README.md), and [operations runbooks](docs/ops_runbooks/README.md). Older version-specific documents are retained as historical design records, not automatically as current operational truth.

## Security and licensing

Never commit `.env`, API keys, tokens, or production credentials. Review authentication, authorization, network exposure, and service configuration before deployment.

The existing repository documentation describes the project as **proprietary**; no open-source license grant is asserted here.
