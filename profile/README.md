<p align="center">
  <img src="banner.svg" alt="Rack'n Roll — self-hosted home lab · automated newsrooms" width="640">
</p>

<p align="center"><em>Infrastructure as code, pipelines as code — everything reproducible from the repos in this org.</em></p>

<p align="center">
  <a href="https://github.com/racknroll/homelab/actions/workflows/ci.yml"><img src="https://github.com/racknroll/homelab/actions/workflows/ci.yml/badge.svg?branch=main" alt="homelab CI"></a>
  <a href="https://github.com/racknroll/usevimli.dev/actions/workflows/build.yml"><img src="https://github.com/racknroll/usevimli.dev/actions/workflows/build.yml/badge.svg?branch=main" alt="usevimli.dev build"></a>
  <a href="https://github.com/racknroll/tatami.usevimli.dev/actions/workflows/test.yml"><img src="https://github.com/racknroll/tatami.usevimli.dev/actions/workflows/test.yml/badge.svg?branch=main" alt="tatami.usevimli.dev test"></a>
</p>

## Live

| | |
|---|---|
| 📰 [usevimli.dev](https://usevimli.dev) | Tech newsroom — bilingual (TR/EN) daily briefings and articles |
| 🥊 [tatami.usevimli.dev](https://tatami.usevimli.dev) | Combat-sports newsroom — Turkish-language fight news |

## What lives here

| Repo | What it is |
|---|---|
| [`homelab`](https://github.com/racknroll/homelab) | The lab itself: hypervisor, containers, services, CI, monitoring — declared, applied and tested from this one repo |
| [`usevimli.dev`](https://github.com/racknroll/usevimli.dev) | Tech newsroom — content pipeline, site and automation |
| [`tatami.usevimli.dev`](https://github.com/racknroll/tatami.usevimli.dev) | Combat-sports newsroom — same pipeline, same discipline |

*Repos are private — this page is the public front door.*

## How it works

- **Newsrooms** — an [n8n](https://n8n.io) pipeline collects sources, drafts stories with
  GLM, runs fact-check and house-style gates, then publishes. Sites build and deploy on
  every push; every story carries its edit notes with it.
- **Infrastructure** — services, versions and configuration live in the `homelab` repo.
  Nightly jobs converge the lab to the declared state; CI runs on self-hosted runners
  inside the lab, not on borrowed machines.
- **Operations** — least-privilege tokens and deploy keys, no secrets in git, snapshots
  before every change, alerts on every failure.

## The newsroom pipeline

```mermaid
flowchart LR
    S["Sources<br/>RSS & feeds"] --> N["n8n<br/>pipeline"]
    N --> D["GLM<br/>drafting"]
    D --> G{"Gates<br/>fact-check +<br/>house style"}
    G -- pass --> B["Astro build<br/>Cloudflare Pages"]
    G -- fail --> E["edit notes,<br/>rework"]
    E --> D
    B --> W1["usevimli.dev"]
    B --> W2["tatami.usevimli.dev"]

    classDef good stroke:#1a7f37
    classDef bad stroke:#cf222e
    class B,W1,W2 good
    class E bad
```

## The lab

```mermaid
flowchart TB
    GH["GitHub Actions"] --> R["self-hosted runners<br/>inside the lab"]
    R -- "publish images" --> H["Harbor<br/>+ Trivy scans"]
    R -- "build & deploy" --> S["services<br/>n8n · Prometheus · Alertmanager · …"]
    H -- "pull images" --> S
    A["homelab-apply<br/>nightly converge"] --> S
    H --- NAS["Synology NAS<br/>registry blobs + backups"]
```

## Stack

- **Infrastructure:** Proxmox VE · Terraform · LXC · self-hosted GitHub Actions runners
- **Automation:** n8n · GLM
- **Sites:** Astro · Cloudflare Pages
- **Registry:** Harbor · Trivy
- **Monitoring:** Prometheus · Alertmanager
- **Networking & storage:** Tailscale · Synology NAS

## Principles

1. If it is not in the repo, it does not exist.
2. Automation writes, humans approve.
3. Test before it matters, not after.
