# Change Management

## Purpose

Provide production-style control for container and infrastructure changes while keeping the process proportionate to risk.

The model is influenced by ITIL change enablement principles: maximise successful changes by assessing risk, authorising appropriately, scheduling sensibly and retaining evidence.

## Change Types

### Standard Change

A low-risk, repeatable and well-understood change with a proven implementation and rollback procedure.

Examples:

- patch-level update to a stateless exporter;
- approved cAdvisor version update after the update process has been proven;
- documentation-only changes.

Typical controls:

- automated validation;
- pre-authorised execution;
- automated health check;
- rollback to previous known-good image.

### Normal Change

A change requiring assessment because impact or uncertainty is meaningful.

Examples:

- Portainer upgrade;
- Authelia configuration change;
- Prometheus major/minor update;
- reverse-proxy change;
- persistent-volume or network changes.

Typical controls:

- documented reason;
- impact and risk assessment;
- implementation plan;
- rollback plan;
- approval;
- backup evidence where applicable;
- post-deployment verification.

### Emergency Change

An urgent change required to restore service or remediate a serious security exposure.

Examples:

- critical exploited vulnerability;
- failed authentication service;
- urgent reverse-proxy/security remediation.

Typical controls:

- accelerated approval;
- minimum necessary scope;
- evidence captured during or immediately after implementation;
- mandatory retrospective review.

## Required Change Record

For normal and emergency changes, retain at least:

- Change ID or Git PR/commit reference.
- Date/time.
- Requestor/implementer.
- Affected host(s).
- Affected service(s).
- Current version/configuration.
- Proposed version/configuration.
- Reason/business or technical benefit.
- Risk and expected impact.
- Backup/pre-flight status.
- Implementation steps.
- Validation criteria.
- Rollback plan.
- Approval decision.
- Actual result.
- Follow-up actions or lessons learned.

## Approval Model

Approval should be proportionate to risk.

| Risk | Example | Approval |
|---|---|---|
| Low | Stateless exporter patch | Pre-authorised after proven process |
| Medium | Application minor upgrade | Review before deployment |
| High | Auth, proxy, monitoring core | Explicit approval and backup checks |
| Critical | Major platform/data migration | Planned maintenance and tested recovery path |

## Change Windows

Changes with potential customer or platform impact should be scheduled when monitoring is available and rollback can be performed immediately.

Avoid high-risk changes when:

- backup status is unknown;
- the previous working image is unavailable;
- health checks are not defined;
- there is insufficient time to validate or roll back;
- multiple unrelated high-risk changes would overlap.

## Update Flow

```text
WUD / dependency automation detects update
              ↓
Git change or Pull Request
              ↓
Risk classification
              ↓
Automated validation and security checks
              ↓
Approval where required
              ↓
Targeted deployment
              ↓
Health / smoke / monitoring validation
              ↓
Close change or execute rollback
```

## Configuration Drift

Direct production edits create drift between Git and the running environment. During migration they may still occur, but once Git becomes authoritative, direct edits should be treated as exceptions and reconciled back into source control immediately.

## Post-Implementation Review

A review is required when:

- a deployment fails;
- rollback is required;
- an unexpected outage occurs;
- a database/schema migration changes rollback behaviour;
- an emergency change is performed;
- the implementation reveals a reusable improvement to the standard process.

The review should focus on improving controls and documentation rather than assigning blame.
