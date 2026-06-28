# Nexlayer — dagster

<!-- nexlayer:meta version=1 analyzed=2026-06-28T00:13:49Z repo=https://github.com/armondhonore/dagster branch=master -->

> **For AI agents (Claude Code, Cursor, Gemini CLI, Copilot):**
> This file is the **project context** for this Nexlayer deployment — tech stack, env vars, secrets, live URL.
> For full platform detail (nexlayer.yaml schema, Dockerfile rules, CI/CD, task recipes) read **`nexlayer.skills`** in this repo.
>
> **Critical rules (full detail in `nexlayer.skills`):**
> - Inter-pod refs: `${podName:port}` only — never `localhost` or bare hostnames
> - Docker Hub images: prefix with `mirror.gcr.io/library/` — bare tags fail on the cluster
> - Secrets: set in the Nexlayer dashboard — never commit to `nexlayer.yaml` or Dockerfile
>
> **This file:** `agent-managed` sections update automatically. `user-editable` sections (Local Development Setup, Nexlayer Deployment Plan, Build Notes) are yours — preserved across re-analysis.

## Project Summary
<!-- nexlayer:section agent-managed=project_summary -->
Dagster is a cloud-native data pipeline orchestrator that provides integrated lineage, observability, and a declarative programming model for data engineering workflows.
<!-- nexlayer:end -->

## Technology Stack
<!-- nexlayer:section agent-managed=tech_stack -->
| Name | Kind | Version | Detected From |
|------|------|---------|---------------|
| Python | language | 3.10+ | pyproject.toml |
| Dagster | framework | latest | README.md |
| PostgreSQL | database | 15 | helm |
| uv | tool | latest | uv.lock |
| Pytest | tool | latest | pyproject.toml |
<!-- nexlayer:end -->

## Repository Structure
<!-- nexlayer:section agent-managed=structure_map -->
- python_modules/ — Core Dagster backend logic
- js_modules/ — Frontend/UI components
- helm/ — Kubernetes deployment configurations
- integration_tests/ — System-level testing
- examples/ — Sample pipeline implementations
<!-- nexlayer:end -->

## External Services Required
<!-- nexlayer:section agent-managed=external_deps -->
Services that must be configured separately (not deployed by Nexlayer):

- PostgreSQL (for run storage and event logging)
<!-- nexlayer:end -->

## Local Development Setup
<!-- nexlayer:section user-editable=local_setup -->
### Prerequisites

- Python >= 3.10
- uv
- PostgreSQL

### Environment variables

Copy `.env.example` to `.env.local` and fill in:

```
DAGSTER_HOME=~/.dagster
DATABASE_URL=postgresql://user:pass@localhost:5432/dagster
```

### Steps

1. `uv sync` — Install Python dependencies using uv
2. `dagster dev` — Launch the Dagster development environment including the Webserver and Daemon

<!-- nexlayer:end -->

## Nexlayer Setup
<!-- nexlayer:section agent-managed=nexlayer_setup -->
### Pod Environment Variables

| Pod | Variable | Value | Kind |
|-----|----------|-------|------|
| `app` | `command` | `sh -c "mkdir -p $DAGSTER_HOME && exec dagster-webserver --empty-workspace -h 0.0.0.0 -p 3000"` | plain |
| `app` | `DAGSTER_HOME` | `/opt/dagster/dagster_home` | plain |

### nexlayer.yaml

```yaml
application:
  name: dagster
  pods:
  - name: app
    image: mirror.gcr.io/dagster/dagster-k8s:1.11.14
    path: /
    command: sh -c "mkdir -p $DAGSTER_HOME && exec dagster-webserver --empty-workspace -h 0.0.0.0 -p 3000"
    servicePorts:
    - 3000
    vars:
      DAGSTER_HOME: /opt/dagster/dagster_home
```

<!-- nexlayer:end -->

## Nexlayer Deployment Plan
<!-- nexlayer:section user-editable=deployment_plan -->
### Pod Topology

| Pod | Image | Port | Role |
|-----|-------|------|------|
| dagster-webserver | mirror.gcr.io/library/python:3.11-slim | 3000 | web |
| dagster-daemon | mirror.gcr.io/library/python:3.11-slim | 0 | scheduler |
| dagster-db | mirror.gcr.io/library/postgres:15-alpine | 5432 | database |

### Deployment notes

- The dagster-webserver and dagster-daemon both connect to the database using dagster-db.pod:5432
- The Daemon pod handles scheduling and run queue management independently of the UI pod
- User-defined code deployments (User Code Server) should be deployed as additional worker pods following the same .pod networking pattern

<!-- nexlayer:end -->

## Build Notes
<!-- nexlayer:section user-editable=build_notes -->
<!-- Add notes for future builds here — preserved across re-analysis -->
<!-- nexlayer:end -->

## Nexlayer Configuration
<!-- nexlayer:section agent-managed=nexlayer_config -->
**Last deployed:** 2026-06-28T00:18:57Z  
**Live URL:** https://relaxed-weasel-dagster.cloud.nexlayer.ai  
**Runtime:**  · **Port:** auto-detected  
**Deploy branch:** master  

```yaml
application:
  name: dagster
  pods:
  - name: app
    image: mirror.gcr.io/dagster/dagster-k8s:1.11.14
    path: /
    command: sh -c "mkdir -p $DAGSTER_HOME && exec dagster-webserver --empty-workspace -h 0.0.0.0 -p 3000"
    servicePorts:
    - 3000
    vars:
      DAGSTER_HOME: /opt/dagster/dagster_home
```
<!-- nexlayer:end -->

## Build History
<!-- nexlayer:section agent-managed=build_history -->
| Date | Status | Notes |
|------|--------|-------|
| 2026-06-28T00:13:49Z | analyzed | initial repo analysis |
| 2026-06-28T00:18:57Z | success | deployed https://relaxed-weasel-dagster.cloud.nexlayer.ai |
<!-- nexlayer:end -->
