# 01 | Network Foundation 🏗️

Establishing a known-good internal network to support future monitoring and incident-response operations.

---

## 1. Purpose & Objectives 🎯

The purpose of this phase is to deploy and validate the internal network for the NOC Operations Simulation.

The network was designed to provide:

- VLAN segmentation
- 802.1Q trunking
- Inter-VLAN routing
- DHCP addressing
- Dedicated device management
- SSH remote access
- Unused-port isolation
- A known-good network baseline

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

The router and switch were configured to establish the internal network baseline. Full device configurations are available in the `config/` folder.

### 🔀 RTR-01 — Router Configuration

`RTR-01` serves as the Layer 3 device for the internal network.

The following was configured during the Network Foundation phase:

- Device hostname and basic management settings
- 802.1Q subinterfaces for router-on-a-stick
- VLAN 10 gateway — `10.10.10.1/24`
- VLAN 20 gateway — `10.10.20.1/24`
- VLAN 99 as the native VLAN
- Inter-VLAN routing between USERS and MANAGEMENT
- DHCP services for VLAN 10
- DHCP address exclusions for infrastructure addresses
- SSH version 2 for remote management
- Local administrative account and VTY access
- Interface descriptions

📄 [View RTR-01 Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/Phase%201%20RTR-01)

### 🔌 SW-01 — Switch Configuration

`SW-01` provides Layer 2 connectivity and segmentation for the internal network.

The following was configured during the Network Foundation phase:

- Device hostname and basic management settings
- VLAN 10 — USERS
- VLAN 20 — MANAGEMENT
- VLAN 99 — PARKING-NATIVE
- VLAN 99 as the native VLAN
- `Gi1/0/1` as an 802.1Q trunk to `RTR-01`
- `Gi1/0/10` as an access port for `STORE-PC1` in VLAN 10
- VLAN 20 management SVI — `10.10.20.2/24`
- Default gateway — `10.10.20.1`
- SSH version 2 for remote management
- Local administrative account and VTY access
- Unused interfaces assigned to VLAN 99 and administratively disabled
- Interface descriptions

📄 [View SW-01 Configuration](configs/Phase 1 SW-01)

---

## 4. Validation & Troubleshooting 🔎

The completed network was tested before establishing the baseline.

| Validation | Result |
| --- | --- |
| Physical connectivity | PASS ✅ |
| 802.1Q trunk | PASS ✅ |
| VLAN segmentation | PASS ✅ |
| Inter-VLAN routing | PASS ✅ |
| DHCP | PASS ✅ |
| RTR-01 SSH | PASS ✅ |
| SW-01 SSH | PASS ✅ |
| Unused-port isolation | PASS ✅ |

Wireshark was used to verify DHCP, ARP, and ICMP traffic. The complete DHCP DORA process — **Discover → Offer → Request → Acknowledge** — was successfully captured.

During testing, two issues were identified and resolved:

- Wi-Fi and wired Ethernet created competing routes on `STORE-PC1`. Disabling Wi-Fi allowed lab traffic to follow the intended wired path.
- Windows OpenSSH initially rejected the legacy SSH algorithms supported by the Cisco IOS devices. After adjusting client compatibility settings, SSH access succeeded.

These tests confirmed that the internal network was functioning as designed.

---

## 5. Results & Handoff 🏁

The Network Foundation phase established a working and documented internal network with VLAN segmentation, routing, DHCP, management access, SSH, and endpoint connectivity.

This known-good baseline will be used as a reference when monitoring and incident-response scenarios are introduced later in the project.

🌐 Next Phase: WAN & Internet Connectivity

