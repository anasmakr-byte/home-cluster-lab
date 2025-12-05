# Home Cluster Lab 🏠🧪

Modern homelab and network built for:
- Hands-on practice with **service provider networks** (FTTH/Broadband/VoIP)
- Experimenting with **virtualization, monitoring, automation, and AI**
- Hosting real services used daily at home

I work as an **L2 Network Support Technician (FTTH/Broadband/VoIP/FWA)** and this homelab is where I prototype, test, and break things before they ever touch production.

---

## 🔧 High-Level Architecture

- **Access & Edge**
  - Cosmote **1G FTTH** connection
  - **FRITZ!Box 5690 XGS** as ISP router  
  - Ubiquiti **UDM Pro** set as *exposed host* behind FRITZ!Box

- **Core Switching & Wi-Fi**
  - **USW Flex XG** 1G switch (core)
    - 1G uplinks to:
      - UDM Pro
      - Main PC (2.5G NIC)
      - Two U6 APs (Wi-Fi 6)
      - USW 16 PoE
  - **USW 16 PoE** → alarm system, TV, NVR, USW Lite 8 PoE, both Proxmox nodes etc.
  - **USW Lite 8 PoE** → Raspberry Pi (Pi-hole)
  - ~30 IoT devices on dedicated VLAN

- **Virtualization / Compute**
  - **Proxmox Cluster** (`home-cluster`) with 2 nodes:
    - **proxmox1**
      - Intel i7-8700 (12 threads), 40 GB RAM, 1 TB SSD, Nvidia GT 1030
    - **proxmox2**
      - Intel i5-10400 (12 threads), 64 GB RAM, 1 TB NVMe + 4 TB HDD, RTX 4060 Ti

## 📚 Documentation

- [Network topology](docs/01-network-topology.md)

---

## 🧱 VLAN & Network Design

Logical VLAN segmentation:

- **VLAN 1 – Home** (default)
- **VLAN 20 – IoT**
- **VLAN 30 – Guest Wi-Fi**
- **VLAN 40 – Surveillance (NVR + cameras)**
- **VLAN 50 – Media**
- **VLAN 99 – Management**

Key goals:
- Keep **IoT** and **Guest** traffic isolated from core LAN.
- Place **NVR and cameras** on dedicated Surveillance VLAN.
- Centralize **management access** (UDM Pro, switches, hypervisors) on VLAN 99.

---

## 🖥️ Proxmox Cluster & Guests

### Node: `proxmox1`

Hardware:
- Intel i7-8700 (12 threads)
- 40 GB DDR4
- 1 TB SSD
- Nvidia GT 1030

Key Guests:

**LXCs**
- `101` – Pi-hole (DNS / ad-blocking)
- `102` – Uptime Kuma (service status)
- `104` – n8n (automation / workflows)
- `105` – Vaultwarden (password manager)
- `107` – Prometheus (metrics)
- `108` – Grafana (dashboards)
- `109` – Proxmox PVE exporter
- `110` – NGINX Proxy Manager (reverse proxy)
- `120` – Home Assistant (home automation)

**VMs**
- `100` – Ubuntu test VM (lab, usually off)
- `103` – Windows 11 test VM (lab, usually off)
- `130` – Wazuh SIEM (security) 
- `190` – Kali test VM (lab, testing)

---

### Node: `proxmox2`

Hardware:
- Intel i5-10400 (12 threads)
- 64 GB DDR4
- 1 TB NVMe
- 4 TB HDD (media)
- RTX 4060 Ti (GPU passthrough)

Key Guests:

**LXCs**
- `200` – CasaOS (running:
  - Plex
  - Prowlarr
  - Radarr
  - Sonarr
  - Transmission
  - Overseerr
)
- `299` – Proxmox PVE exporter

**VMs**
- `290` – `ai-core` (Ubuntu 24.04, GPU passthrough)
  - **Ollama** with:
    - `llama3.1:latest`
    - `phi3:mini`
- `230` – Valheim game server

---

## 📊 Monitoring & Observability

Monitoring stack:

- **Prometheus** (LXC on proxmox1) for metrics collection
- **Proxmox PVE exporters** on both nodes
- **Grafana** dashboards for:
  - Cluster overview (CPU, RAM, disk, VM/LXC status)
  - Hardware health (temperatures, disks)
- **Uptime Kuma** to monitor:
  - Public entrypoints (reverse proxy)
  - Internal services (Pi-hole, Home Assistant, media services, AI VM)
- **Wazuh SIEM** on proxmox1 (VM 130) for:
  - Centralized security event and log monitoring
  - Agents on proxmox1, proxmox2, Raspberry Pi 4
  - Ingesting logs from the UDM Pro firewall/router

---

## 🌐 Reverse Proxy & External Access

- **NGINX Proxy Manager** fronting (sanitized list):
  - Grafana
  - Prometheus (when needed)
  - Home Assistant
  - Vaultwarden
  - Media stack (Plex, etc. – with care)
- Uses **dynamic DNS** (DuckDNS) on the FRITZ!Box → UDM Pro → services.
- All externally exposed services:
  - Use **HTTPS** with valid certificates
  - Are **behind authentication** and restricted as much as possible

> Real domain names, IPs, and sensitive hostnames are intentionally redacted in this public repo.

---

## 🤖 AI & Automation

### `ai-core` VM (GPU passthrough)

- OS: Ubuntu 24.04
- GPU: RTX 4060 Ti (8 GB VRAM)
- Stack:
  - **Ollama**:
    - `llama3.1:latest`
    - `phi3:mini`
- Use-cases:
  - Local LLM experimentation
  - Testing prompts and workflows for homelab automation
  - Private AI assistant for documentation & troubleshooting

### n8n Workflows

- Automated alerts from Prometheus/Uptime Kuma
- Periodic checks on:
  - Service availability
  - Backup completion
- Future: orchestrating Home Assistant + AI + monitoring events

---

## 📺 Media & Storage

- **CasaOS** LXC on proxmox2 handles:
  - Plex (media server)
  - Sonarr / Radarr / Prowlarr (media automation)
  - Transmission (downloads)
  - Overseerr (request management)
- **Storage**:
  - 4 TB HDD on proxmox2 dedicated to media library

---

## 🔐 Security & Best Practices

High-level practices:

- **Segmentation** via VLANs for IoT, Guests, Surveillance, Media, Management.
- **DNS** filtering via Pi-hole.
- **Password manager** (Vaultwarden) + strong unique credentials.
- Regular **backups and snapshots** in Proxmox.
- All public exposure via:
  - Reverse proxy
  - TLS
  - 2FA where possible

⚠️ This repo **never** contains:
- Real passwords / secrets / tokens
- Real public IP addresses
- Real dynamic DNS domains

---

## 🧭 Roadmap / Next Steps

- [ ] Add sample Ansible playbooks for LXC/VM provisioning.
- [ ] Publish (sanitized) Grafana dashboards as JSON exports.
- [ ] Add scripts for automated backup verification.
- [ ] Add more detailed documentation on network policies & firewall rules.

---

## 📫 Contact

- **LinkedIn:** *https://www.linkedin.com/in/anastasios-makris-998371398/*
