# VLANs and Network Segmentation

This homelab uses VLANs to separate different types of traffic and reduce the blast radius of misconfigurations or compromised devices.

> Note: IP addressing is intentionally omitted/sanitized in this public repo.

---

## VLAN Overview

| VLAN ID | Name         | Purpose                                | Typical Devices                                  |
|--------:|--------------|----------------------------------------|--------------------------------------------------|
| 1       | Home         | Main LAN for personal devices          | PCs, laptops, consoles, some wired devices       |
| 20      | IoT          | Isolated smart/IoT devices             | Smart plugs, lights, sensors, cameras (if any)   |
| 30      | Guest        | Internet-only guest Wi-Fi              | Phones and laptops of visitors                   |
| 40      | Surveillance | NVR and camera network                 | NVR, IP cameras                                  |
| 50      | Media        | Media services and related storage     | Plex, *arr stack, download services              |
| 99      | Management   | Management plane for infra             | UDM Pro, switches, Proxmox hosts, monitoring     |

---

## Design Goals

- **Reduce risk from IoT**  
  IoT devices are placed in **VLAN 20** so they can reach the internet and required local services, but not freely access the main LAN.

- **Keep cameras and NVR separated**  
  Surveillance devices live in **VLAN 40**, isolating camera traffic and keeping the NVR away from user devices and general internet browsing.

- **Guest Wi-Fi is internet-only**  
  Guest clients in **VLAN 30** should not be able to:
  - Discover LAN devices
  - Reach Proxmox, management interfaces, or storage

- **Dedicated management plane**  
  **VLAN 99** is used for:
  - UDM Pro
  - Switches
  - Proxmox hypervisors
  - Monitoring (Prometheus/Grafana, Uptime Kuma, etc.)

  Access to this VLAN is restricted to specific admin devices and accounts.

- **Media separated from core LAN**  
  Media services (Plex, *arr stack, download clients) can be placed in **VLAN 50** to keep them logically separate from Home and Management networks.

---

## Example Policy Ideas (High Level)

> This is a high-level description, not a full firewall rule export.

- **VLAN 20 (IoT)**
  - Allowed → Internet
  - Allowed → Specific services (e.g., Home Assistant, NTP, DNS)
  - Blocked → Direct access to VLAN 1, 50, 99 (except needed services)

- **VLAN 30 (Guest)**
  - Allowed → Internet only
  - Blocked → All other internal VLANs

- **VLAN 40 (Surveillance)**
  - Allowed → NVR ↔ Cameras
  - Limited access from Management for viewing/maintenance

- **VLAN 99 (Management)**
  - Allowed → Reach all infra devices as needed (UDM, switches, Proxmox, monitoring)
  - Restricted to admin devices/users

This segmentation lets the homelab grow without turning into a flat, insecure LAN.
