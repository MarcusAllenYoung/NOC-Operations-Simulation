# 01 | Network Deployment 🏗️

Establishing a known-good internal network to support future monitoring and incident-response operations.

---

## 1. Purpose & Objectives 🎯

The purpose of this phase is to deploy and validate the internal network for the NOC Operations Simulation. This foundation exists to support the monitoring and incident-response work in later phases

| Design Goal | Purpose |
| --- | --- |
| VLAN segmentation & trunking | Logical separation of users and management traffic |
| Inter-VLAN routing | Connectivity between segmented networks |
| DHCP addressing | Automated IP assignment for endpoints |
| SSH & device hardening | Secure remote administration |
| Known-good baseline | Reference point for later monitoring and incident-response work |

---

## 2. Network Design 🗺️

### 🛠️ Equipment Used

| Equipment | Model / Platform | Role |
| --- | --- | --- |
| Router | Cisco ISR 2911 | Routing and DHCP |
| Switch | Cisco Catalyst 2960 | VLANs, trunking, and Layer 2 connectivity |
| Workstation | Windows PC | User endpoint, SSH, testing, and packet capture |
| Cabling | Cat5e/Cat6 | Physical network connectivity |

### ⛓️ Physical Topology

<img width="4280" height="3406" alt="Physical Network Topology" src="https://github.com/user-attachments/assets/d56cc878-b6e6-4a8e-9a93-3c87da51f04e" />

### 🔀 Logical Topology

<img width="1292" height="277" alt="Logical Network Topology" src="https://github.com/user-attachments/assets/707745e1-d562-45d1-a87e-ad772241c088" />

WAN connectivity is excluded from this phase and will be introduced during Phase 2.

### 🔌 Interface Mapping

| Device | Interface | Role |
| --- | --- | --- |
| RTR-01 | Gi0/1 | Trunk to SW-01 |
| RTR-01 | Gi0/1.10 | VLAN 10 gateway |
| RTR-01 | Gi0/1.20 | VLAN 20 gateway |
| RTR-01 | Gi0/1.99 | Native VLAN |
| SW-01 | Gi1/0/1 | Trunk to RTR-01 |
| SW-01 | Gi1/0/10 | STORE-PC1 |
| SW-01 | Vlan20 | Switch management |
| SW-01 | Remaining ports | Unused / shutdown |

### 🌐 VLAN & IP Addressing

| VLAN | Name | Network | Gateway | Purpose |
| ---: | --- | --- | --- | --- |
| 10 | USERS | 10.10.10.0/24 | 10.10.10.1 | User endpoints |
| 20 | MANAGEMENT | 10.10.20.0/24 | 10.10.20.1 | Device management |
| 99 | PARKING-NATIVE | N/A | N/A | Native VLAN / unused ports |

---

## 3. Network Implementation ⚙️

Both devices received baseline hardening: hostname, local admin account, SSH v2 for remote management, VTY access, and interface descriptions.

### 🔀 RTR-01 — Router

Layer 3 routing for the internal network.

- 802.1Q subinterfaces for router-on-a-stick (VLANs 10, 20, native 99)
- Inter-VLAN routing between USERS and MANAGEMENT
- DHCP for VLAN 10, with infrastructure addresses excluded

📄 [View RTR-01 Phase 1 Configurations](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/Phase%201%20Running-Config)

### 🔌 SW-01 — Switch

Layer 2 segmentation and access.

- VLANs 10 (USERS), 20 (MANAGEMENT), and 99 (native/parking)
- `Gi1/0/1` trunk to RTR-01
- `Gi1/0/10` access port for STORE-PC1
- Management SVI `10.10.20.2/24`
- Unused ports assigned to VLAN 99 and administratively disabled

📄 [View SW-01 Phase 1 Configurations](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/SW-01%20-%20Switch/Running-Config)

---

## 4. Validation & Troubleshooting 🔎

### 🌐 DHCP & Endpoint Verification

STORE-PC1 successfully received its network configuration from RTR-01 through DHCP.

The workstation received:

- IP address: 10.10.10.21/24
- Default gateway: 10.10.10.1
- DNS suffix: noc.lab

<img width="1906" height="227" alt="Image" src="https://github.com/user-attachments/assets/a7c243a1-8449-4377-89f0-4867aed5be59" />

---

Wireshark captured the complete DHCP process used by STORE-PC1 to obtain its address: Discover, Offer, Request, and ACK.

<img width="1918" height="1031" alt="Image" src="https://github.com/user-attachments/assets/bdcfbd58-fe82-497e-aaa6-b91d7b4ec3f7" />

### 📡 Connectivity Verification

Connectivity was verified from STORE-PC1 to the VLAN 10 gateway `10.10.10.1` , and the SW-01 management interface `10.10.20.2`.

**STORE-PC1 → VLAN 10 Gateway**

<img width="1915" height="399" alt="Image" src="https://github.com/user-attachments/assets/26d811b7-7e61-437a-9b3d-308381903bda" />

**STORE-PC1 → SW-01 Management Interface**

<img width="1918" height="355" alt="Image" src="https://github.com/user-attachments/assets/11346c69-3f7a-499d-aa89-6d62855b6783" />

### 🔐 SSH Verification & Troubleshooting

Remote management was tested from STORE-PC1 to both RTR-01 and SW-01.

During initial testing, SSH connections failed because the older Cisco devices use legacy SSH algorithms that the newer Windows SSH client does not enable by default.

The required SSH algorithm was enabled on STORE-PC1, resolving the compatibility issue. SSH access to both devices was then successfully verified.

**RTR-01 SSH Verification**

<img width="1912" height="213" alt="Image" src="https://github.com/user-attachments/assets/0a729927-7198-4e70-8d2f-30df929490b7" />

**SW-01 SSH Verification**

<img width="1918" height="239" alt="Image" src="https://github.com/user-attachments/assets/6db5d04c-0f47-4215-9c58-b1e88aefa482" />

---

## 5. Results & Handoff 🏁

The Network Foundation phase established a working and documented internal network with VLAN segmentation, routing, DHCP, management access, SSH, and endpoint connectivity.

This known-good baseline will be used as a reference when monitoring and incident-response scenarios are introduced later in the project.

🌐 Next Phase: [WAN & NAT Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/WAN%20&%20NAT%20Configuration.md)

