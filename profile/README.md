# dawg-io

Self-hosted tooling for GitHub Actions at scale, Home Assistant, AI-assisted development, and GitOps on Kubernetes.

Everything here runs in a real homelab before it gets published. Repos are documented to the point where someone else can actually deploy them.

---

## ActionsManager

A control plane for managing GitHub Actions workflows across many repositories from one place — sync workflows, detect configuration drift, and ship changes as reviewable pull requests.

| Repo | What it is |
|---|---|
| [**actions-manager**](https://github.com/dawg-io/actions-manager) | The application. Python + React, runs as a single container. **Free self-hosted beta** — no paid plans, no hosted SaaS yet. |
| [**am-build-vars**](https://github.com/dawg-io/am-build-vars) | Composite Action that reads a committed `am-build-vars.yml` and exports each key as an env var, so one byte-identical workflow builds differently per repo. |

```bash
docker run -d --name actions-manager -p 8080:8080 \
  -v actions-manager-data:/app/data \
  -e INSTALLATION_MODE=self-hosted \
  -e SECRET_KEY=<openssl rand -hex 32> \
  -e ALLOW_INSECURE_HTTP=true \
  ghcr.io/dawg-io/actions-manager:latest
```

Docs: [actionsmanager.io](https://actionsmanager.io) · Demo: [multi-repo rollout, end to end](https://youtu.be/WkDYK7pCBjI)

> Beta software. Self-hosted operators are responsible for securing the deployment, protecting GitHub credentials, and reviewing workflow changes before they land. PR-based delivery is recommended over direct commit.

---

## Home Assistant

Two HACS default integrations, plus a dashboard card and blueprints — most of them built to make infrastructure visible from inside Home Assistant.

| Repo | What it is | HACS |
|---|---|---|
| [**noaa_it_all**](https://github.com/dawg-io/noaa_it_all) | NOAA solar data, hurricane tracking, and location-specific rip current and surf zone forecasts as sensors and images. | Default |
| [**homeassistant-fluxcd**](https://github.com/dawg-io/homeassistant-fluxcd) | Exposes FluxCD resources in a Kubernetes cluster as HA sensor entities, each as its own device. Uses `kubernetes-asyncio`. | Default |
| [**fluxcd-topology-card**](https://github.com/dawg-io/fluxcd-topology-card) | Lovelace card that graphs relationships between Kustomizations, HelmReleases, and their sources. Requires the integration above. | Custom repo (Dashboard) |
| [**homeassistant-myfuelportal**](https://github.com/dawg-io/homeassistant-myfuelportal) | Propane/fuel tank level, gallons remaining, and current price from the MyFuelPortal customer portal. | Custom repo |
| [**home-assistant-blueprints**](https://github.com/dawg-io/home-assistant-blueprints) | HVAC blueprints: seasonal thermostat scheduling and door/window protection. | — |

*Not affiliated with, endorsed by, or sponsored by NOAA.*

---

## Claude Skills

[**claude-skills**](https://github.com/dawg-io/claude-skills) — [Claude Code](https://claude.com/claude-code) skills for application development and infrastructure work. Each skill has a `SKILL.md`, its own README covering when it fires and how it works phase by phase, and an explicit list of what it refuses to do.

| Skill | What it does | Scope |
|---|---|---|
| `code-development` | Takes a feature, bugfix, or patch from request to open PR — scope, implement, document, in that order. | General-purpose |
| `ci-pipeline` | Watches CI after a push or PR, reads the real failing logs, root-causes it, then fixes or escalates with options. | General-purpose |
| `release` | Drives a release end to end — PR audit, blocking docs gate, approval issue, workflow dispatch, announcement. | General-purpose |
| `ansible-create` | Turns a host into a minimal playbook, an inventory line, and a draft PR. Ships a working example repo. | General-purpose |
| `terraform-create` | Interviews you for a new Proxmox VM and emits a ready-to-paste tfvars entry plus the matching Ansible inventory line. | Environment-specific |

General-purpose skills discover your toolchain rather than assuming one. Each has an `init` that writes a small `.claude/<skill>.yml` so the next run reads instead of re-deriving.

---

## Kubernetes, Kustomize, and FluxCD

[**fluxcd-ezy**](https://github.com/dawg-io/fluxcd-ezy) — a sanitized reference implementation of a real multi-cluster FluxCD setup on k0s. Covers production and staging from one repo, two secret management patterns (External Secrets + Sealed Secrets), MetalLB and Traefik on bare metal, Ceph storage, cert-manager with DNS-01, and Renovate for chart and image bumps. All secrets, credentials, and addresses are replaced with labeled placeholders.

Standalone Kustomize bases, each usable directly with `kubectl apply -k` or consumed by a FluxCD `Kustomization`:

| Repo | Deploys |
|---|---|
| [home-assistant-kustomize](https://github.com/dawg-io/home-assistant-kustomize) | Home Assistant (StatefulSet, PVC, optional Traefik IngressRoute) |
| [zigbee2mqtt-kustomize](https://github.com/dawg-io/zigbee2mqtt-kustomize) | Zigbee2MQTT |
| [zwave-js-ui-kustomize](https://github.com/dawg-io/zwave-js-ui-kustomize) | Z-Wave JS UI |
| [mosquitto-kustomize](https://github.com/dawg-io/mosquitto-kustomize) | Eclipse Mosquitto (MQTT) |
| [mealie-kustomize](https://github.com/dawg-io/mealie-kustomize) | Mealie |
| [n8n-kustomize](https://github.com/dawg-io/n8n-kustomize) | n8n |
| [ups-nut-kustomize](https://github.com/dawg-io/ups-nut-kustomize) | Network UPS Tools (NUT) |
| [ookla-speedtest-kustomize](https://github.com/dawg-io/ookla-speedtest-kustomize) | Speedtest Tracker |

Most include a `flux/gotk-sync.yaml` with ready-to-use `GitRepository` and `Kustomization` manifests.

---

## Notes

Most repos are MIT licensed; check each one. ActionsManager is under its own license and is in beta — see its README for current status and limits.

Issues and pull requests are welcome on any repo. Installation steps, configuration, and release status live in each repository's own README.
