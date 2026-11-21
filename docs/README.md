# Nested AI documentation

This is the documentation entry point for the **V6.6 recovery baseline** of the former OmniPath project. Historical references to OmniPath v2/v3/v4, Citadel, and other milestones are preserved to avoid rewriting project history.

## Current orientation

- [Full readiness plan](READINESS_PLAN.md) — November 21–22 operational checkpoint, readiness gates, evidence requirements, and advancement toward recursive verification.
- [Recovery and verification status](RECOVERY_STATUS.md) — baseline commits, what has been verified, and outstanding runtime risks.
- [Repository README](../README.md) — current project identity, setup, and navigation.
- [Testing guide](TESTING_GUIDE.md) — test commands; historical version labels and examples require source verification.
- [Developer guide](developer_guide/README.md) — development concepts and workflows.
- [User guide](user_guide/README.md) — agent and mission concepts.
- [Operations runbooks](ops_runbooks/README.md) — operational procedures; validate against the selected Compose file before deployment.
- [API reference](api/README.md) — generated/historical API materials; verify endpoints against the running FastAPI OpenAPI output.

## Historical design and evidence

- [Architecture](ARCHITECTURE.md) — originally a **v3.0 design proposal**, not an independently verified description of the recovered runtime.
- [Roadmap V4](ROADMAP_V4.md) and the dated design files — historical planning.
- [Mission suite](mission_suite_v1.md), [phase-six proof missions](phase6_proof_missions.md), and [proof results](proof_mission_results.md) — historical evidence; do not infer a fresh successful run.
- [Coverage gaps](test_coverage_gaps.md) — historical analysis; remeasure against the current commit.

## Documentation standard

Treat source code and fresh runtime evidence as authoritative for current behavior. Label proposals as proposals, tests as tests, and actual deployments as deployments. Do not equate the existence of infrastructure configuration with operational readiness. Keep the V6.6 recovery baseline and newer development distinct.
