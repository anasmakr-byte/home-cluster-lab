# Proxmox Cluster Overview

The homelab runs a 2-node **Proxmox VE** cluster named `home-cluster`.

---

## Nodes

### proxmox1

- **CPU:** Intel i7-8700 (12 threads)
- **RAM:** 40 GB DDR4
- **Storage:** 1 TB SSD
- **GPU:** Nvidia GT 1030

**Role:** Core infrastructure services (DNS, monitoring, reverse proxy, automation, home automation, security testing).

---

#### LXCs on proxmox1

- `101` – **Pi-hole**  
  DNS and ad-blocking for the network.

- `102` – **Uptime Kuma**  
  Monitors homelab services and public endpoints.

- `104` – **n8n**  
  Automation platform for workflows and alerts.

- `105` – **Vaultwarden**  
  Self-hosted password manager.

- `107` – **Prometheus**  
  Metrics collection for hosts and services.

- `108` – **Grafana**  
  Dashboards for metrics visualization.

- `109` – **Proxmox PVE exporter**  
  Exposes proxmox1 metrics to Prometheus.

- `110` – **NGINX Proxy Manager**  
  Reverse proxy and SSL termination for exposed web services.

- `120` – **Home Assistant**  
  Home automation and smart device integration.

---

#### VMs on proxmox1

- `100` – **Ubuntu test VM**  
  Used for experiments and testing (usually powered off).

- `103` – **Windows 11 test VM**  
  Used for GUI-based testing and lab scenarios (usually powered off).

- `190` – **Kali Linux**  
  Dedicated **cybersecurity testing VM** used for:
  - Running security tools and scanners
  - Practicing penetration testing in isolated lab environments
  - Capture-the-flag style exercises and training

---

### proxmox2

- **CPU:** Intel i5-10400 (12 threads)
- **RAM:** 64 GB DDR4
- **Storage:** 1 TB NVMe (system and fast storage)
- **Additional Storage:** 4 TB HDD (media library)
- **GPU:** RTX 4060 Ti (GPU passthrough)

**Role:** Media, AI workloads, and game server.

---

#### LXCs on proxmox2

- `200` – **CasaOS**  
  Hosts:
  - Plex
  - Prowlarr
  - Radarr
  - Sonarr
  - Transmission
  - Overseerr

- `299` – **Proxmox PVE exporter**  
  Exposes proxmox2 metrics to Prometheus.

---

#### VMs on proxmox2

- `290` – **ai-core** (Ubuntu 24.04, GPU passthrough)  
  - Runs **Ollama** with local LLMs:
    - `llama3.1:latest`
    - `phi3:mini`
  - Used for AI experimentation, prompts, and private assistance.

- `230` – **Valheim server**  
  Dedicated game server for Valheim.

---

## Cluster Features

- Centralized management via the Proxmox web UI.
- Shared monitoring with Prometheus + Grafana.
- Mix of **LXCs** for lightweight services and **VMs** where full OS isolation, GUI, or GPU passthrough is needed.
- Clear separation of roles:
  - `proxmox1` → infra + monitoring + automation + security testing.
  - `proxmox2` → media + AI + gaming.
