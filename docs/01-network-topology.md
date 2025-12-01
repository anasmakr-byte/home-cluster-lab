# Network Topology

```mermaid
graph TD
  Internet["Internet / Cosmote 1G FTTH"]
  Fritz["FRITZ!Box 5690 XGS"]
  UDM["UDM Pro (Gateway and VLAN Router)"]
  FlexXG["USW Flex XG Core Switch (1G)"]
  SW16["USW 16 PoE (1G)"]
  SW8["USW Lite 8 PoE (1G)"]

  AP1["U6 AP 1 (Wi-Fi 6)"]
  AP2["U6 AP 2 (Wi-Fi 6)"]
  IoTDevices["~30 IoT Devices"]

  PC["Main PC (2.5G NIC)"]

  Alarm["Home Alarm System"]
  TV["TV"]
  NVR["NVR + 8 Cameras"]

  PVE1["Proxmox1 - Core Services"]
  PVE2["Proxmox2 - Media and AI"]
  Pi["Pi-hole (Raspberry Pi)"]

  %% WAN and Edge
  Internet --> Fritz
  Fritz --> UDM

  %% Core switches
  UDM --> FlexXG
  FlexXG --> SW16
  SW16 --> SW8

  %% Wi-Fi and IoT
  FlexXG --> AP1
  FlexXG --> AP2
  AP1 --- IoTDevices
  AP2 --- IoTDevices

  %% Wired client on core
  FlexXG --> PC

  %% Devices on USW 16 PoE
  SW16 --> Alarm
  SW16 --> TV
  SW16 --> NVR
  SW16 --> PVE1
  SW16 --> PVE2

  %% Devices on USW Lite 8 PoE
  SW8 --> Pi
