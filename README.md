# Hey, I'm Rahat 👋
**DevOps Engineer** | Building and breaking infrastructure in public | Kubernetes, GitOps, Home Lab

📍 Manitoba, Canada &nbsp;•&nbsp; [LinkedIn](https://www.linkedin.com/in/rahatahsan/) &nbsp;•&nbsp; [Twitter/X](https://x.com/RahatAhsan20) &nbsp;•&nbsp; [Medium](https://medium.com/@s.rahatahsan)

---

## About Me

I build and operate self-hosted infrastructure on Kubernetes, with a background in data analytics. Currently deepening my skills through the **Kubecraft internship program**.

How systems work under the hood, how they scale, how they communicate, and why they break. That curiosity is what drives me. Kubernetes and cloud infrastructure is where it all comes together.

---

## What I'm Working On

- 🏠 **Homelab** — a 2-node Raspberry Pi k3s cluster with full GitOps via Flux, NAS-backed iSCSI and NFS storage, and tested node failover
- 🐘 **Stateful workloads** — HA PostgreSQL cluster via CloudNative-PG with streaming replication, automated failover, and off-cluster S3 backups validated under real iSCSI session loss
- 📊 **Monitoring** — Prometheus, Grafana, and Alertmanager migrated off SD cards to durable iSCSI LUNs after 28 combined restarts wiped all history
- ⚙️ **Kubernetes** — scheduling, networking, storage, security contexts, resource management, and production-style manifests
- 🖥️ **Linux** — custom Arch Linux environment with Hyprland, portable dotfiles via chezmoi, and a reproducible dev workflow
- **CKA (Certified Kubernetes Administrator)** — in progress
- **AWS Solutions Architect Associate (SAA)** — in progress
- **AKS project** — applying homelab patterns (GitOps, HA, CSI storage) to Azure Kubernetes Service to build cloud platform experience

---

## 🏠 [Homelab](https://github.com/AhsanRahat12/Homelab)

Self-hosted infrastructure running on a 2-node Raspberry Pi k3s cluster, managed declaratively with Flux. The Git repo is the single source of truth. No manual `kubectl apply`, no SSH-ing into nodes. Flux handles the rest.

Storage is deliberately separated from compute. Persistent data lives on a QNAP NAS via iSCSI LUNs and NFS shares, not on the nodes. democratic-csi manages the attach/detach lifecycle automatically. If a node fails, the pod reschedules and storage follows. Single node failure is survivable across every production workload.

Stateful workloads run on a dedicated HA PostgreSQL cluster with backups streaming to S3 storage that lives outside Kubernetes. If the cluster dies, the backups survive.

**Hardware**

| Node | Hardware | Role |
|------|----------|------|
| `zoro` | Raspberry Pi 5 (16GB) | Control plane |
| `luffy` | Raspberry Pi 5 (16GB) | Worker node |
| `nas` | QNAP TS-464 | iSCSI LUNs, NFS shares, Minio S3 |

**Network:** Private overlay via Tailscale. External access via Cloudflare Tunnel, no open ports, no port forwarding.

**Stack:** K3s · Flux · Helm · Kustomize · Traefik · democratic-csi · CloudNative-PG · Barman Cloud · cert-manager · Minio · Prometheus · Grafana · SOPS + Age · Renovate · Cloudflare Tunnel

---

## Hosted Projects

### 🔖 [Linkding](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/linkding)
Self-hosted bookmark manager. Storage went through three stages: SD card → static iSCSI PV pinned to one node → democratic-csi with no nodeSelector. The migration removed the single point of failure entirely. Failover tested, pod reschedules to either node, LUN follows automatically.

### 📚 [Audiobookshelf](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/audiobookshelf)
Self-hosted audiobook and podcast server. Four volumes with a deliberate storage split: config and metadata on iSCSI (block storage, RWO, exclusive access), audiobooks and podcasts on NFS (network share, RWX, mounts on any node). Zero volumes on SD card in production. Failover tested across both nodes.

### 🧭 [Homepage](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/homepage)
Cluster startpage that links every service in one place and pulls live node CPU, memory, and pod counts straight from the Kubernetes API via a dedicated read-only ClusterRole. The deliberate choice here was zero persistence: config lives entirely in Git as a ConfigMap, so there's no PV to drift out of sync with the repo and no second source of truth. Hardened with PSA baseline enforcement, non-root execution, and all Linux capabilities dropped.

### 💰 [Actual Budget](https://github.com/AhsanRahat12/Homelab/blob/main/pi-zoro/docs/actual-budget)
Self-hosted personal finance app using envelope budgeting. Single pod, single iSCSI LUN, SQLite on block storage — no PostgreSQL dependency. Deployed with PSA restricted enforced from day one. The interesting problem: a fresh iSCSI LUN formats as root:root. Kubernetes fsGroup changes group ownership only — not user ownership — so a non-root container running as UID 1000 hits EACCES on startup trying to write to the volume root. Fixed with a one-time root initContainer after temporarily relaxing the namespace to PSA baseline, then locked back to restricted. The chown is permanent on the LUN's filesystem — no root access needed on subsequent restarts.

### 📊 [kube-prometheus-stack](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/Kube-Prometheus-Stack)
Full observability stack. Prometheus, Grafana, and Alertmanager were originally writing to SD cards via emptyDir. 28 combined pod restarts wiped all metrics, dashboards, and silence rules before migration. All three components now run on dedicated iSCSI LUNs. Metrics survive restarts and redeployments. 30-day retention set explicitly to prevent unbounded TSDB growth.

### 🐘 [CloudNative-PG](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/cnpg)
Two-instance HA PostgreSQL cluster managed by the CloudNative-PG operator. Primary on one node, replica on the other, with streaming replication and automated failover. Built to replace per-app SQLite databases that were one iSCSI hiccup away from corruption.

Continuous WAL archiving to Minio via the Barman Cloud plugin. Daily base backups with 30-day retention, preferring the standby to avoid load on the primary. Minio runs on the QNAP outside Kubernetes — if the cluster dies, the backup target stays alive. HA failover was validated under a real iSCSI session drop during deployment: the primary lost storage, the replica was promoted within seconds, no data loss.

**[🔥 Incidents & Lessons](https://github.com/AhsanRahat12/Homelab#-incidents--lessons)** — real production failures, root causes, and fixes from running this cluster. Links to app specific incidents that were encountered and successfully fixed.

---

## Tech Stack

| Category | Tools |
|----------|-------|
| Infrastructure & DevOps | Kubernetes · K3s · Flux · Helm · Kustomize · Traefik · Docker · Linux (Arch) · Git |
| Storage | democratic-csi · iSCSI · NFS · QNAP · Minio |
| Databases | PostgreSQL · CloudNative-PG · Barman Cloud |
| Observability | Prometheus · Grafana · Alertmanager |
| Security & Secrets | SOPS · Age · cert-manager · Cloudflare Tunnel · Tailscale |
| Automation | Renovate · GitHub Actions |
| Languages | Python · Bash |
| Tooling | Hyprland · chezmoi · mise · DevPod · Obsidian · Vim |

---

## Other Repositories

### ⚙️ Dev Environment
- **[dotfiles](https://github.com/AhsanRahat12/dotfiles)** — managed with chezmoi. One command bootstraps mise, global tooling, and all configurations on any machine
- **[DevContainer](https://github.com/AhsanRahat12/DevContainer)** — reproducible, project-scoped development environments using DevPod and mise

### 🚀 Projects
- **[Arch Linux Setup](https://github.com/AhsanRahat12/arch-linux-setup)** — installation guide covering manual partitioning, LVM, full-disk encryption, and Hyprland
- **[Dockerized Python Apps](https://github.com/AhsanRahat12/Dockerized-Python-Projects)** — Python applications containerized with Docker for portable deployments

### 📓 Documentation
- **[devops-lab](https://github.com/AhsanRahat12/devops-lab)** — Kubernetes manifests, troubleshooting docs, and notes from hands-on experimentation
- **[social-media-archive](https://github.com/AhsanRahat12/social-media-archive)** — technical writing published on LinkedIn and X covering Kubernetes, Docker, Linux, and DevOps
