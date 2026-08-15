# Contributing and Change Rules

## Purpose

Infrastructure changes are operational changes. Contributions should therefore be small, reviewable, attributable and recoverable.

## Working Principles

- Git is the source of truth.
- One logical infrastructure change per pull request where practical.
- Do not mix unrelated service upgrades.
- Do not commit secrets or runtime data.
- Document risk and rollback for production-affecting changes.
- Validate before deployment and verify after deployment.
- Prefer evidence over assumption.

## Pull Request Expectations

A production-affecting pull request should state:

- affected host;
- affected service/stack;
- current state/version;
- proposed state/version;
- reason for change;
- change classification: Standard, Normal or Emergency;
- risk/impact;
- testing performed;
- backup requirement/status;
- deployment steps;
- validation criteria;
- rollback method.

## Review Standard

Review should confirm:

- no secret material is present;
- Compose/configuration syntax validates;
- image/version references are intentional;
- security implications have been considered;
- host targeting is correct;
- stateful migration implications are understood;
- monitoring/health checks exist;
- rollback is realistic.

## Production Deployment

Merging a change and deploying a change are separate control points where risk warrants it. High-impact services may require an explicit Jenkins approval gate after merge or before production execution.

## Emergency Changes

Emergency changes may use an accelerated path, but must still be committed/reconciled to Git and reviewed retrospectively. Urgency does not remove the need for traceability.

## Improvement Culture

Failed changes and incidents should feed improvements into tests, runbooks and automation. Reviews should focus on system/process improvement rather than blame.
