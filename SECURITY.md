# Security

## Repository Security Principles

This repository must contain configuration and documentation only. It must not become a copy of runtime state or a secret store.

## Never Commit

- `.env` files containing credentials or tokens;
- API tokens;
- passwords;
- private SSH keys;
- TLS private keys;
- Docker secrets;
- application databases;
- Prometheus data;
- Portainer databases;
- generated backups;
- log archives containing sensitive information;
- cloud credentials;
- authentication session material.

## Secret Handling

Secrets should be supplied at runtime through one of the following approved patterns:

1. Docker secrets where supported.
2. Host-resident environment files excluded from Git.
3. A dedicated secrets-management platform when introduced.

Compose files may reference secret names or variables, but must not contain the secret value.

Example:

```yaml
environment:
  CLOUDFLARE_API_TOKEN: ${CLOUDFLARE_API_TOKEN}
```

is acceptable provided the value itself remains outside Git.

## Pre-Commit Review

Before importing any existing Compose/configuration file:

- inspect it for literal passwords and tokens;
- inspect referenced configuration files;
- remove or parameterise sensitive values;
- confirm `.gitignore` coverage;
- run secret scanning before push where tooling is available.

## Image Security

- Prefer explicit third-party version tags to `latest`.
- Review release notes before high-impact upgrades.
- Scan candidate images with Trivy or equivalent before deployment.
- Do not deploy a new image solely because an update exists.
- Retain a known-good rollback reference.

## Container Hardening

Where compatible with the application, prefer:

- `read_only: true`;
- `no-new-privileges:true`;
- dropping unnecessary capabilities;
- read-only mounts;
- non-root users;
- narrow host port exposure;
- isolated Docker networks;
- limited Docker socket access.

Access to `/var/run/docker.sock` is effectively privileged and must be treated accordingly.

## Change Security

High-risk/security-sensitive services require stronger change controls, particularly:

- authentication;
- reverse proxy;
- IDS/security monitoring;
- vulnerability management;
- container management;
- monitoring core;
- anything that stores or migrates persistent state.

## Repository Visibility

The repository must be reviewed before any internal hostnames, addresses, topology details or operational configuration are published. If production configuration is to be stored here, private visibility is preferred unless the content has been deliberately sanitised for public release.

## Incident Response

If a secret is committed:

1. Treat it as compromised.
2. Rotate/revoke it immediately.
3. Remove it from the repository and history as appropriate.
4. Review access/audit logs.
5. Record the incident and preventive action.

Deleting the visible line from the latest commit is not sufficient secret remediation.
