# 01 | Network Deployment 🏗️

Phase 1 establishes the internal branch network that will serve as the foundation for the NOC Operations Simulation.

The goal of this phase is to create a known-good network before introducing upstream Internet connectivity, centralized monitoring, alerting, and simulated incidents in later phases.

---

## 1. Purpose & Objectives 🎯

The physical network was designed with separate user and management networks, centralized DHCP addressing, inter-VLAN routing, and secure remote device administration.

| Design Goal | Purpose |
| --- | --- |
| VLAN segmentation | Separate user and network-management traffic |
| 802.1Q trunking | Carry multiple VLANs between SW-01 and RTR-01 |
| Inter-VLAN routing | Provide Layer 3 connectivity between VLANs |
| DHCP | Automatically configure user endpoints |
| Management network | Provide dedicated addressing for network-device management |
| SSH | Provide secure remote administration |
| Port hardening | Disable and isolate unused switch interfaces |
| Known-good baseline | Establish normal network behavior before monitoring and incident simulation |

---

## 2. Network Design 🗺️

### 🛠️ Equipment Used

| Equipment | Model / Platform | Role |
| --- | --- | --- |
| RTR-01 | Cisco ISR 2911 | Inter-VLAN routing and DHCP |
| SW-01 | Cisco Catalyst 2960X | VLAN segmentation and Layer 2 switching |
| STORE-PC1 | MacBook | Branch user endpoint |
| Cabling | Ethernet | Physical network connectivity |

> NOC-SRV01 and upstream Internet connectivity are not part of the Phase 1 network state. They are introduced during later phases.

---

### ⛓️ Physical Topology

[INSERT UPDATED PHASE 1 PHYSICAL TOPOLOGY]

```text
STORE-PC1
   MacBook
      |
      | Ethernet
      |
   Gi1/0/10
     SW-01
      |
   Gi1/0/1
      |
      | 802.1Q Trunk
      |
    Gi0/1
    RTR-01
  
