# Hey, I'm Rahat 👋
**DevOps Engineer** | Building and breaking infrastructure in public | Kubernetes, GitOps, Home Lab
🔍 Open to Platform Engineering and Cloud Infrastructure roles

📍 Manitoba, Canada &nbsp;•&nbsp; [LinkedIn](https://www.linkedin.com/in/rahatahsan/) &nbsp;•&nbsp; [Twitter/X](https://x.com/RahatAhsan20) &nbsp;•&nbsp; [Medium](https://medium.com/@s.rahatahsan)

---

## About Me

I started in Environmental Science — chemistry, hydrology, GIS, data pipelines. That's where I first wrote code: R for data visualization, spatial analysis to make sense of messy field data. I got hooked on the feeling of taking raw input and turning it into something meaningful.

That led to a data analyst role. Good work, but I kept bumping into the edges of my own understanding — where is this database actually running? How does it connect to everything else? What actually breaks when something breaks?

Those questions pulled me into DevOps. I've been following that curiosity ever since — building a homelab, running Kubernetes in production on Azure, and learning how the pieces of modern infrastructure fit together and stay running.

By day I'm still a data analyst. After hours I'm debugging clusters.

---

## Tech Stack

| Category | Tools |
|----------|-------|
| Infrastructure & DevOps | Kubernetes · K3s · AKS · Flux · Terraform · Helm · Kustomize · Traefik · Docker · Linux (Arch) · Git |
| Storage | democratic-csi · iSCSI · NFS · QNAP · Minio · Azure Blob Storage |
| Databases | PostgreSQL · CloudNative-PG · Barman Cloud |
| Observability | Prometheus · Grafana · Alertmanager · Telegram alerting |
| Security & Secrets | SOPS · Age · cert-manager · Azure Key Vault · Cilium · Cloudflare Tunnel · Tailscale |
| Automation | Renovate · GitHub Actions · Terraform |
| Languages | Python · Bash · HCL |
| Tooling | Hyprland · chezmoi · mise · DevPod · Obsidian · Vim |

---

## What I'm Working On

- ☁️ **Cloudlab** — multi-tenant SaaS platform on AKS: Terraform provisions cloud resources and generates GitOps manifests, Flux keeps the cluster in sync
- 🐘 **Stateful workloads** — HA PostgreSQL with CloudNative-PG, streaming replication, automated failover, and off-cluster backups validated under real iSCSI session loss
- 📊 **Observability** — Prometheus, Grafana, and alerting wired end-to-end across homelab and cloud environments
- 🔁 **CI/CD** — starting to build automation pipelines with GitHub Actions
- 🐍 **Python** — automating infrastructure tasks and tooling
- 📜 **CKA (Certified Kubernetes Administrator)** — in progress
- ☁️ **AWS Solutions Architect Associate (SAA)** — in progress

---

## ☁️ [Cloudlab](https://github.com/AhsanRahat12/Cloudlab)

A multi-tenant SaaS platform on Azure Kubernetes — every customer gets their own isolated app, database, and backups. Provisioned by Terraform, kept in sync by Flux. Adding a new customer is a one-line change.

Built to take homelab patterns (GitOps, HA databases, CSI secrets, network isolation) and apply them at cloud scale with production-grade tooling.

**How it works:** Terraform provisions the AKS cluster, Key Vault, and storage — then generates all the Kubernetes manifests for each customer and writes them into this repo. Flux picks them up and applies them. Nothing is deployed by hand.

**What each customer gets:**
- Isolated namespace locked down with Kubernetes' strictest security profile
- Dedicated PostgreSQL cluster with continuous WAL archiving and daily backups to Azure Blob
- n8n workflow automation app — non-root, read-only filesystem, no extra permissions
- HTTPS at `<customer>.cloudlab.rahatahsan.com` via Traefik + Let's Encrypt
- Network policy that prevents any customer from reaching another customer's database
- Metrics wired into Prometheus with Grafana alerts routed to Telegram

**Stack:** Terraform · AKS · Flux · Cilium · Traefik · cert-manager · CloudNative-PG · Barman Cloud · Azure Key Vault (CSI) · kube-prometheus-stack · Grafana alerting

**[🔥 Engineering Challenges](https://github.com/AhsanRahat12/Cloudlab#-engineering-challenges)** — real infrastructure problems hit while building this: cross-namespace Flux blocks, Traefik IngressClass mismatches, disk slot exhaustion, and more.

---

## 🏠 [Homelab](https://github.com/AhsanRahat12/Homelab)

Self-hosted infrastructure on a 2-node Raspberry Pi k3s cluster, managed declaratively with Flux. The Git repo is the single source of truth — no manual `kubectl apply`, no SSH-ing into nodes.

Storage is separated from compute. Persistent data lives on a QNAP NAS via iSCSI LUNs and NFS shares. democratic-csi manages attach/detach automatically. Single node failure is survivable across every production workload.

**Hardware**
| Node | Hardware | Role |
|------|----------|------|
| `zoro` | Raspberry Pi 5 (16GB) | Control plane |
| `luffy` | Raspberry Pi 5 (16GB) | Worker node |
| `nas` | QNAP TS-464 | iSCSI LUNs, NFS shares, Minio S3 |

**Network:** Private overlay via Tailscale. External access via Cloudflare Tunnel — no open ports, no port forwarding.

**Stack:** K3s · Flux · Helm · Kustomize · Traefik · democratic-csi · CloudNative-PG · Barman Cloud · cert-manager · Minio · Prometheus · Grafana · SOPS + Age · Renovate · Cloudflare Tunnel

---

## Hosted Projects

### 🔖 [Linkding](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/linkding)
Self-hosted bookmark manager. Storage migrated through three stages — SD card → static iSCSI PV → democratic-csi with no nodeSelector. Failover tested: pod reschedules to either node, LUN follows automatically.

### 📚 [Audiobookshelf](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/audiobookshelf)
Self-hosted audiobook server. Deliberate storage split: config on iSCSI (RWO, exclusive access), media on NFS (RWX, any node). Zero volumes on SD card in production. Failover tested across both nodes.

### 🧭 [Homepage](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/homepage)
Cluster startpage pulling live node CPU, memory, and pod counts from the Kubernetes API via a read-only ClusterRole. Zero persistence by design — config lives entirely in Git as a ConfigMap. Hardened with PSA baseline, non-root execution, and all capabilities dropped.

### 💰 [Actual Budget](https://github.com/AhsanRahat12/Homelab/blob/main/pi-zoro/docs/actual-budget)
Self-hosted personal finance app. PSA restricted from day one. Key problem: fresh iSCSI LUN formats as root:root — Kubernetes fsGroup changes group ownership only, not user ownership, so a non-root UID 1000 container hits EACCES on startup. Fixed with a one-time root initContainer after temporarily relaxing to PSA baseline, then locked back to restricted.

### 📊 [kube-prometheus-stack](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/Kube-Prometheus-Stack)
Full observability stack. Originally writing to SD cards via emptyDir — 28 combined pod restarts wiped all metrics and dashboards. Migrated all three components to dedicated iSCSI LUNs. 30-day retention set explicitly to cap TSDB growth.

### 🐘 [CloudNative-PG](https://github.com/AhsanRahat12/Homelab/tree/main/pi-zoro/docs/cnpg)
Two-instance HA PostgreSQL cluster. Primary on one node, replica on the other, streaming replication and automated failover. Continuous WAL archiving to Minio via Barman Cloud. HA failover validated under real iSCSI session loss — replica promoted within seconds, no data loss.

**[🔥 Incidents & Lessons](https://github.com/AhsanRahat12/Homelab#-incidents--lessons)** — real production failures, root causes, and fixes.

---

## Other Repositories

### ⚙️ Dev Environment
- **[dotfiles](https://github.com/AhsanRahat12/dotfiles)** — managed with chezmoi. One command bootstraps mise, global tooling, and all configurations on any machine
- **[DevContainer](https://github.com/AhsanRahat12/DevContainer)** — reproducible, project-scoped development environments using DevPod and mise

### 🚀 Projects
- **[Cloudlab](https://github.com/AhsanRahat12/Cloudlab)** — multi-tenant SaaS platform on Azure AKS with GitOps, Terraform-generated manifests, and per-customer isolation
- **[Arch Linux Setup](https://github.com/AhsanRahat12/arch-linux-setup)** — installation guide covering manual partitioning, LVM, full-disk encryption, and Hyprland
- **[Dockerized Python Apps](https://github.com/AhsanRahat12/Dockerized-Python-Projects)** — Python applications containerized with Docker for portable deployments

### 📓 Documentation
- **[devops-lab](https://github.com/AhsanRahat12/devops-lab)** — Kubernetes manifests, troubleshooting docs, and notes from hands-on experimentation
- **[social-media-archive](https://github.com/AhsanRahat12/social-media-archive)** — technical writing published on LinkedIn and X covering Kubernetes, Docker, Linux, and DevOps

---

## Let's Connect

I write about what I'm building and learning — the wins, the failures, and the 2am debugging sessions.

If you're on a similar path — switching careers, coming from a non-traditional background, figuring it out as you go — I'd love to connect. The best way to follow along:

- 💼 **LinkedIn:** [Rahat Ahsan](https://www.linkedin.com/in/rahatahsan/) — career updates and reflections
- 🐦 **X:** [@RahatAhsan20](https://x.com/RahatAhsan20) — day to day learning and quick wins
- ✍️ **Medium:** [@s.rahatahsan](https://medium.com/@s.rahatahsan) — longer form writing on the career switch and technical deep dives
- 🐙 **GitHub:** [AhsanRahat12](https://github.com/AhsanRahat12) — everything I'm building ← *you are here* 😊

> *"From soil samples to CI/CD pipelines."*
