# Homelab Infrastructure

Production-style infrastructure management for a two-host Docker environment.

This repository is intended to become the **source of truth** for container deployment definitions, change control, validation, deployment and rollback. Runtime data, credentials and secrets remain outside Git.

## Goals

- Manage Docker infrastructure as version-controlled configuration.
- Pin third-party container images to explicit versions wherever practical.
- Detect upstream updates without allowing uncontrolled production changes.
- Validate changes through CI before deployment.
- Deploy only the affected service and host.
- Verify health after every deployment.
- Maintain a tested rollback path.
- Keep secrets and persistent application data out of source control.

## Environment

The environment currently uses two Docker hosts:

- **TestServer** — primary application, monitoring, management, availability and proxy/authentication workloads.
- **ids-01** — security-focused workloads including IDS/security monitoring and vulnerability-management services.

See [CONTAINERS.md](CONTAINERS.md) for the current inventory and [ARCHITECTURE.md](ARCHITECTURE.md) for the target repository structure.

## Target Change Flow

```text
Upstream image release
        ↓
Update discovery (WUD / dependency automation)
        ↓
Git change / Pull Request
        ↓
Compose validation
        ↓
Security and policy checks
        ↓
Approval
        ↓
Jenkins deployment to affected host
        ↓
Health and smoke tests
        ↓
Grafana / operational verification
        ↓
Rollback if required
```

## Core Principle

> Git is the source of truth. Production should be deployed from reviewed configuration rather than edited directly on a running host.

## Documentation

- [PROJECT_PLAN.md](PROJECT_PLAN.md) — phased implementation plan.
- [ARCHITECTURE.md](ARCHITECTURE.md) — host and repository architecture.
- [CONTAINERS.md](CONTAINERS.md) — current container inventory.
- [SECURITY.md](SECURITY.md) — repository and deployment security requirements.
- [CHANGE_MANAGEMENT.md](CHANGE_MANAGEMENT.md) — update and approval process.
- [DEPLOYMENT.md](DEPLOYMENT.md) — controlled deployment runbook.
- [ROLLBACK.md](ROLLBACK.md) — rollback principles and procedure.

## Current Status

The project is in the **baseline and discovery** phase. Third-party images are being moved away from floating `latest` tags, existing Compose definitions are being identified, and the current two-host deployment is being documented before automation is introduced.

## Safety

Do not commit `.env` files, API tokens, passwords, private keys, certificates containing private material, Docker secrets, application databases, monitoring data or backup archives. See [SECURITY.md](SECURITY.md) and `.gitignore` before adding infrastructure files.
