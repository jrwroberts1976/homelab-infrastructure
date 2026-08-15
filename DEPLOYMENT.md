# Deployment Runbook

## Purpose

Provide a repeatable production-style deployment process for container changes across the two Docker hosts.

## Preconditions

Before deployment:

- approved change exists where required;
- target host and service are identified;
- Compose configuration validates;
- candidate image is available;
- security scan is acceptable;
- backup is current for stateful/high-risk services;
- rollback reference is known;
- monitoring is available;
- sufficient time exists to verify or roll back.

## Standard Deployment Sequence

1. Confirm the current running version.
2. Confirm the Git change and affected host.
3. Validate configuration.
4. Pull the candidate image.
5. Record the previous image/version.
6. Deploy only the affected service.
7. Wait for application startup/migrations.
8. Run service-specific health checks.
9. Check logs for startup errors.
10. Verify Prometheus/Grafana recovery where applicable.
11. Record the outcome.

Example validation:

```bash
docker compose config >/dev/null
```

Example targeted deployment:

```bash
docker compose pull <service>
docker compose up -d <service>
```

## Deployment Success Criteria

A successful Docker command does not by itself mean a successful change.

The change is successful only when appropriate acceptance criteria pass, such as:

- container remains running;
- health status becomes healthy;
- expected port is listening;
- endpoint responds correctly;
- application can perform its primary function;
- monitoring target returns to UP;
- no critical startup errors are present;
- dependent services remain healthy.

## Stateful Services

For a stateful deployment:

- verify backup before starting;
- understand database/schema migration behaviour;
- identify whether downgrade is supported;
- capture application-specific migration logs;
- do not assume image rollback will reverse data migrations.

## Multi-Host Control

Deployments must be targeted to the host owning the changed stack. A TestServer change must not restart unrelated services on ids-01, and vice versa.

## Failed Deployment

If validation fails:

1. stop further changes;
2. collect container state and logs;
3. decide whether the service can safely remain on the new version;
4. invoke the documented rollback plan when appropriate;
5. verify restored service health;
6. update the change record;
7. perform post-implementation review when warranted.

## Evidence

Jenkins should eventually retain:

- Git commit/PR;
- target host/service;
- validation output;
- security scan result;
- approval;
- deployed image;
- deployment log;
- post-deployment tests;
- rollback outcome if used.
