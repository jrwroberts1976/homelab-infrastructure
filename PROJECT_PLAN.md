# Project Plan

## Purpose

Build a production-style, auditable container lifecycle-management process across two Docker hosts using Git, Jenkins, WUD, security scanning and operational monitoring.

The design follows pragmatic ITIL principles: control risk, maintain accountability, preserve evidence, define rollback, and review outcomes without creating unnecessary process overhead.

## Target Operating Model

```text
Detect → Assess → Approve → Validate → Deploy → Verify → Record → Review
```

Git is the configuration source of truth. Changes should not normally be made directly to live Compose files once the migration is complete.

## Phase 1 — Baseline and Discovery

- Inventory both Docker hosts.
- Map each running container to its owning Compose project and file.
- Record image version, persistence, exposed interfaces, health check and business/technical importance.
- Identify remaining floating image tags.
- Identify existing manual deployment paths.
- Verify backups for stateful workloads.

Exit criteria: documented known-good baseline for both hosts.

## Phase 2 — Repository Structure and Governance

- Establish host-specific configuration directories.
- Add security exclusions and repository rules.
- Define ownership and change classifications.
- Document deployment, rollback and verification standards.
- Import Compose definitions only after secret review.

Exit criteria: Git contains safe configuration definitions and documentation, but no credentials or runtime data.

## Phase 3 — Image and Dependency Policy

- Pin third-party production images to explicit versions where practical.
- Allow intentional floating tags only for documented internal CI/CD deployment channels.
- Use WUD for operational visibility.
- Introduce dependency automation such as Dependabot or Renovate to propose version changes through Git.

Exit criteria: upstream updates create auditable change proposals rather than silent production changes.

## Phase 4 — CI Validation

Build a Jenkins pipeline that performs, at minimum:

1. Changed-file detection.
2. Host and service impact identification.
3. `docker compose config` validation.
4. Image availability checks.
5. Vulnerability scanning.
6. Policy checks.
7. Deployment-risk classification.
8. Approval gate where required.

Exit criteria: invalid or unsafe infrastructure changes cannot proceed to deployment.

## Phase 5 — Controlled Deployment

- Deploy only changed services.
- Target only the affected Docker host.
- Capture deployment timestamps and versions.
- Require current backups for stateful/high-risk applications.
- Keep server/agent version pairs aligned where required.

Exit criteria: deployments are repeatable, targeted and attributable to a Git change.

## Phase 6 — Post-Implementation Validation

Every deployment must prove service restoration rather than treating container start as success.

Validation should include as appropriate:

- container state;
- Docker health check;
- expected listening port;
- HTTP/API response;
- Prometheus target state;
- application-specific functional check;
- startup log error scan;
- Grafana metric recovery.

Exit criteria: deployment success is based on service health, not command exit status alone.

## Phase 7 — Rollback and Recovery

- Define previous known-good image/configuration.
- Distinguish stateless image rollback from stateful schema/data rollback.
- Test rollback using a low-risk service first.
- Record applications where downgrade after migration is unsafe.

Exit criteria: rollback is documented and tested rather than assumed.

## Phase 8 — Operational Visibility

Expose deployment/update metrics to Prometheus and Grafana, including where practical:

- deployed version;
- available version;
- deployment status;
- deployment timestamp;
- backup age;
- health status;
- failed change count.

Exit criteria: current deployment state and update posture are visible operationally.

## Phase 9 — Production-Style Change Management

Introduce pragmatic ITIL-style controls:

- **Standard change:** low-risk, repeatable, pre-authorised update with proven validation and rollback.
- **Normal change:** meaningful version/configuration change requiring assessment and approval.
- **Emergency change:** urgent security or availability remediation with accelerated approval and mandatory retrospective review.

Each significant change should retain evidence of:

- reason;
- affected service/host;
- risk;
- approval where required;
- implementation result;
- validation result;
- rollback decision;
- lessons learned.

## First End-to-End Pilot

Use cAdvisor as the initial production-style update pilot because it is version-pinned, observable, comparatively low risk and easy to validate.

The pilot is complete when an upstream version change can move through:

```text
Detection → Git PR → Jenkins validation → approval → deployment → health check → recorded outcome
```

Only after the process works reliably should it be extended to higher-risk components such as authentication, reverse proxy, Portainer, Prometheus and security services.
