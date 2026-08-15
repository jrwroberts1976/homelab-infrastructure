# Container Inventory

## Purpose

This is the initial service baseline for the two Docker hosts. It is intentionally operational documentation, not a replacement for runtime discovery.

**Important:** versions below reflect containers/configuration confirmed during the August 2026 baseline work. Before Compose definitions are imported into Git, the inventory will be regenerated from each host and reconciled against the running environment.

## TestServer — Confirmed Workloads

| Area | Container / Service | Image / Version | Notes |
|---|---|---|---|
| Dashboards | dashy | `lissy93/dashy:4.5.11` | Explicit version |
| Dashboards | homepage | `ghcr.io/gethomepage/homepage:v1.12.2` | Compose target; runtime should be reverified |
| Availability | uptime-kuma | `louislam/uptime-kuma:1.23.16` | Stateful |
| Availability | autokuma | `ghcr.io/bigboot/autokuma@sha256:8acbd3ad...` | Exact registry digest currently used |
| Availability | smokeping | `linuxserver/smokeping` | LinuxServer image; tagging policy to review |
| Availability | librespeed | `ghcr.io/librespeed/speedtest:6.1.0` | Explicit version |
| Monitoring | prometheus | `prom/prometheus:v3.13.1` | Stateful monitoring core |
| Monitoring | loki | `grafana/loki:2.9.6` | Stateful logging core |
| Monitoring | promtail | `grafana/promtail:2.9.6` | Legacy log shipper; migration status to review |
| Monitoring | node-exporter | `prom/node-exporter:v1.12.1` | Explicit version |
| Monitoring | cadvisor | `ghcr.io/google/cadvisor:0.60.5` | Proposed first update-pipeline pilot |
| Monitoring | blackbox-exporter | `prom/blackbox-exporter:v0.28.0` | Explicit version |
| Monitoring | alloy | `grafana/alloy:v1.18.0` | Stack moved under `docker/stacks`; verify runtime after migration |
| Monitoring | asus-exporter | locally built | Custom exporter |
| Monitoring | crowdsec-exporter | locally built | Custom exporter |
| Security | crowdsec | `crowdsecurity/crowdsec:v1.7.8` | Explicit version |
| Proxy/Auth | nginx-proxy-manager | `jc21/nginx-proxy-manager:2.15.0` | High-impact edge service |
| Proxy/Auth | authelia | `authelia/authelia:4.39.20` | High-impact authentication service |
| Proxy/Auth | duckdns | `lscr.io/linuxserver/duckdns:af6dcae5-ls86` | Explicit LinuxServer build |
| Management | portainer | `portainer/portainer-ce:2.44.0` | Stateful; DB migration-aware |
| Management | portainer_agent | `portainer/agent:2.44.0` | Keep aligned with Portainer server |
| Management | dozzle | `amir20/dozzle:v10.7.1` | Explicit version |
| Management | filebrowser | `filebrowser/filebrowser:v2.63.23` | Stateful |
| Update visibility | wud | `getwud/wud:8` | Update detection/visibility |
| CI/CD | jenkins | `homelab-jenkins:lts-jdk21` | Locally controlled Jenkins image |
| CI/CD | jenkins-docker | `docker:dind` | Docker-in-Docker support |
| Application | birdnet-go | `ghcr.io/tphakala/birdnet-go:20260716` | Explicit version |
| Application | birdnet-exporter | locally built | Custom exporter |
| Application | engineering-portfolio | `james-roberts/engineering-portfolio:latest` | Intentional internal deployment channel |
| Application | maintenance-page | `nginx:alpine` | Maintenance endpoint |
| Application | training-platform | `training-platform:latest` | Intentional local image; verify current runtime |

## ids-01 — Known Security Workloads

The security host is known to run Docker-based security services including:

| Area | Service | Status |
|---|---|---|
| Vulnerability Management | Greenbone / OpenVAS components | Running; exact container/image inventory to capture |
| Vulnerability Management | `gvmd` | Known component; verify image/version |
| Vulnerability Management | `pg-gvm` | Known component; verify image/version |
| Vulnerability Management | Greenbone nginx/web component | Known component; verify image/version |
| Update visibility | WUD | Deployed; verify image/version and Compose ownership |
| Monitoring | cAdvisor / node-exporter / log shipping components | Present in host monitoring design; verify current runtime |

No unverified version numbers are recorded here. The next discovery pass will collect the exact output directly from `ids-01`.

## Inventory Collection Standard

For each Docker host, capture:

```bash
docker ps --format '{{.Names}}\t{{.Image}}\t{{.Status}}'
```

Then map every container back to Compose ownership:

```bash
docker inspect <container> \
  --format 'Project={{index .Config.Labels "com.docker.compose.project"}} File={{index .Config.Labels "com.docker.compose.project.config_files"}}'
```

The Git baseline is accepted only when running containers, Compose definitions and this inventory agree.

## Risk Classification

Initial operational classification:

- **High/Critical:** reverse proxy, authentication, Portainer, Prometheus, Loki, vulnerability/security control components, stateful databases.
- **Medium:** dashboards, Uptime Kuma, Filebrowser, WUD, Jenkins.
- **Low:** exporters and stateless telemetry components such as cAdvisor, node-exporter and blackbox-exporter.

Risk classification controls approval, backup requirements, maintenance timing and rollback depth; it does not imply that low-risk services can be changed without validation.
