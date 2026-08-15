# Architecture

## Overview

The platform currently spans two Docker hosts with different operational roles.

### TestServer

Primary application and platform-services host. Confirmed workloads include monitoring, availability, management, proxy/authentication, dashboards, CI/CD support and locally developed applications.

### ids-01

Security-focused host. Known workloads include IDS/security monitoring, vulnerability-management services and supporting security tooling. The exact container inventory will be verified before importing live Compose definitions.

## Target Repository Layout

```text
homelab-infrastructure/
├── hosts/
│   ├── testserver/
│   │   ├── monitoring/
│   │   ├── management/
│   │   ├── proxy-auth/
│   │   ├── availability/
│   │   ├── dashboards/
│   │   ├── alloy/
│   │   └── ...
│   └── ids-01/
│       ├── security/
│       ├── greenbone/
│       └── ...
├── shared/
│   ├── scripts/
│   ├── tests/
│   └── policy/
├── README.md
├── PROJECT_PLAN.md
├── SECURITY.md
├── CHANGE_MANAGEMENT.md
├── DEPLOYMENT.md
└── ROLLBACK.md
```

## Separation of Concerns

### Git-managed configuration

Suitable for source control after secret review:

- Compose definitions;
- non-sensitive static configuration;
- deployment scripts;
- validation tests;
- monitoring rules and dashboards where appropriate;
- documentation.

### Host-resident runtime data

Must remain outside Git:

- application databases;
- Prometheus TSDB data;
- Portainer state;
- logs;
- container runtime data;
- caches;
- generated state;
- backups.

### Secrets

Must remain outside Git and be injected at runtime through environment files, Docker secrets or another approved secret-management mechanism.

## Deployment Boundaries

Every Compose project has an owning host. CI/CD must determine the affected host from the changed repository path and deploy only there.

Example:

```text
hosts/testserver/monitoring/docker-compose.yml
              ↓
       TestServer only
```

A change to one host must not cause an unrelated restart or deployment on the second host.

## Production Design Principles

- Git is the source of truth.
- Production configuration drift should be detected and corrected.
- Changes should be small and reversible.
- Stateful services receive stronger controls than stateless services.
- Monitoring and authentication components are treated as high impact.
- A container starting is not sufficient evidence of service restoration.
- Deployment evidence should be retained through Git/Jenkins logs and monitoring.

## ITIL Alignment

The architecture supports pragmatic service-management controls:

- clear configuration ownership;
- change traceability;
- risk-based approval;
- implementation planning;
- validation and acceptance criteria;
- rollback planning;
- incident/change correlation;
- post-implementation review where warranted.

The goal is controlled delivery, not process for its own sake.
