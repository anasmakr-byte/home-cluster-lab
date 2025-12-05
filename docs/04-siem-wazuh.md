# Wazuh SIEM

The homelab includes a **Wazuh-based SIEM** deployment for centralized security monitoring, log analysis, and alerting.

---

## Architecture

- **Wazuh Manager**
  - VM ID: `130` on `proxmox1`
  - OS: Ubuntu Server 24.04 LTS
  - Role: Collects and analyzes security events and logs

- **Wazuh Agents**
  - `proxmox1` – Hypervisor and core services
  - `proxmox2` – Media, AI workloads and game server
  - **Raspberry Pi 4** – Pi-hole and other lightweight services

- **Log Sources**
  - **UDM Pro** (firewall/router) sending logs to Wazuh
  - System logs from Proxmox hypervisors and key services
  - Future: application logs from selected containers/VMs

---

## Use Cases

- Monitor authentication events and sudo usage on Proxmox nodes
- Track network-related security events originating from the UDM Pro
- Detect misconfigurations or suspicious behavior on homelab systems
- Practice SIEM usage, rule tuning, and alert triage in a safe environment

---

## Notes

- This SIEM is for **homelab and learning purposes** only.
- Sensitive data (IPs, hostnames, rule details) is intentionally **sanitized** and not exposed in this public repository.
