# Rollback and Recovery

## Principle

Every production change must have a realistic recovery path before deployment begins.

Rollback is not simply "run the old container again". Stateful applications may change database or configuration schemas in ways that make downgrade unsafe.

## Before Change

Record:

- current image/version;
- current Git commit;
- relevant configuration version;
- backup location/status for stateful services;
- application-specific downgrade restrictions;
- validation checks needed after restoration.

## Stateless Rollback

For a stateless service, rollback normally means reverting the Git image/configuration change and recreating only that service.

```text
Revert Git change
      ↓
Validate Compose
      ↓
Pull known-good image
      ↓
Recreate affected service
      ↓
Health verification
```

## Stateful Rollback

For stateful services, determine which recovery case applies:

1. **No data migration occurred** — reverting the image may be sufficient.
2. **Forward-compatible data migration occurred** — application documentation must confirm downgrade support.
3. **Incompatible schema migration occurred** — restore data from the pre-change backup and then restore the previous application version.

Never force an older application against a newer unsupported schema merely to restore service quickly.

## Portainer Lesson

Container-management platforms can migrate their own databases. A server downgrade can fail even when the container image itself is valid. This class of application therefore requires explicit database-version awareness and backup checks before upgrade.

## Rollback Trigger Examples

Rollback should be considered when:

- container enters a restart loop;
- health check remains failed beyond the defined startup period;
- primary application function fails;
- authentication/proxy path fails;
- monitoring indicates significant regression;
- startup logs show migration/configuration failures;
- dependent services become unhealthy.

## Recovery Validation

After rollback, verify:

- expected image/configuration restored;
- container remains running/healthy;
- application endpoint works;
- monitoring returns to normal;
- dependent services recover;
- no data loss is apparent.

## Change Record

A rollback is part of the change outcome, not a separate hidden action. Record the trigger, decision, commands/action taken, restored version, health evidence and follow-up work.

A failed change followed by a successful rollback should still receive a post-implementation review so that the next attempt is safer.
