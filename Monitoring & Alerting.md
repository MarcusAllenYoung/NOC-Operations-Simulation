# 01 | Network Deployment 🏗️

Phase 1 establishes the internal branch network that serves as the foundation for the NOC Operations Simulation.

The goal of this phase is to create and validate a known-good internal network before introducing upstream Internet connectivity, centralized monitoring, alerting, and simulated incidents in later phases.

---

## 1. Purpose & Objectives 🎯

The internal network was designed with separate user and management networks, centralized DHCP addressing, inter-VLAN routing, secure remote device administration, and basic switch-port hardening.

| Design Goal | Purpose |
| --- | --- |
| VLAN segmentation | Separate user and network-management traffic |
| 802.1Q trunking | Carry multiple VLANs between SW-01 and RTR-01 |
| Inter-VLAN routing | Provide Layer 3 connectivity between segmented networks |
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
| STORE-PC1 | Windows PC | Branch user endpoint, SSH testing, and packet capture |
| Cabling | Ethernet | Physical network connectivity |

> Phase 1 focuses only on the internal branch network. Upstream connectivity is introduced in Phase 2, while NOC-SRV01 and centralized monitoring are introduced in Phase 3.

### ⛓️ Physical Topology

<img width="4280" height="3406" alt="Physical Network Topology" src="https://github.com/user-attachments/assets/d56cc878-b6e6-4a8e-9a93-3c87da51f04e" />

### 🔀 Logical Topology

<img width="1292" height="277" alt="Logical Network Topology" src="https://github.com/user-attachments/assets/707745e1-d562-45d1-a87e-ad772241c088" />

WAN connectivity is excluded from this phase and is introduced during Phase 2.

### 🔌 Interface Mapping

| Device | Interface | Role |
| --- | --- | --- |
| RTR-01 | Gi0/1 | 802.1Q trunk to SW-01 |
| RTR-01 | Gi0/1.10 | VLAN 10 default gateway |
| RTR-01 | Gi0/1.20 | VLAN 20 default gateway |
| RTR-01 | Gi0/1.99 | Native VLAN 99 |
| SW-01 | Gi1/0/1 | 802.1Q trunk to RTR-01 |
| SW-01 | Gi1/0/10 | STORE-PC1 access port |
| SW-01 | Vlan20 | Switch management interface |
| SW-01 | Remaining unused ports | VLAN 99 / administratively disabled |

### 🌐 VLAN & IP Addressing

| VLAN | Name | Network | Gateway | Purpose |
| ---: | --- | --- | --- | --- |
| 10 | USERS | `10.10.10.0/24` | `10.10.10.1` | User endpoints |
| 20 | MANAGEMENT | `10.10.20.0/24` | `10.10.20.1` | Network-device management |
| 99 | PARKING-NATIVE | N/A | N/A | Native VLAN and unused interfaces |

### 📍 Device Addressing

| Device / Interface | Address | Assignment |
| --- | --- | --- |
| RTR-01 Gi0/1.10 | `10.10.10.1/24` | Static |
| RTR-01 Gi0/1.20 | `10.10.20.1/24` | Static |
| SW-01 Vlan20 | `10.10.20.2/24` | Static |
| STORE-PC1 | `10.10.10.21/24` | DHCP |

---

## 3. Network Implementation ⚙️

### 🔀 VLAN Segmentation

Three VLANs were created on SW-01 to separate user traffic, management traffic, and unused interfaces.

- VLAN 10 — USERS
- VLAN 20 — MANAGEMENT
- VLAN 99 — PARKING-NATIVE

`Gi1/0/10` was configured as an access interface in VLAN 10 for STORE-PC1.

Unused switch interfaces were assigned to VLAN 99 and administratively disabled.

---

### 🔗 802.1Q Trunking

The connection between SW-01 and RTR-01 was configured as an 802.1Q trunk.

The trunk carries:

- VLAN 10 — USERS
- VLAN 20 — MANAGEMENT
- VLAN 99 — Native

SW-01 `Gi1/0/1` connects to RTR-01 `Gi0/1`.

This trunk provides the Layer 2 path required for multiple VLANs to reach RTR-01 over a single physical connection.

---

### 🌐 Router-on-a-Stick

RTR-01 was configured with 802.1Q subinterfaces to provide Layer 3 gateways for the internal VLANs.

| Subinterface | VLAN | Address | Role |
| --- | ---: | --- | --- |
| Gi0/1.10 | 10 | `10.10.10.1/24` | USERS gateway |
| Gi0/1.20 | 20 | `10.10.20.1/24` | MANAGEMENT gateway |
| Gi0/1.99 | 99 | N/A | Native VLAN |

This configuration allows traffic to be routed between the USERS and MANAGEMENT networks.

---

### 📡 DHCP

RTR-01 provides DHCP service to endpoints in VLAN 10.

Infrastructure addresses from `10.10.10.1` through `10.10.10.20` were excluded from dynamic assignment.

| DHCP Setting | Value |
| --- | --- |
| Network | `10.10.10.0/24` |
| Default Gateway | `10.10.10.1` |
| DNS Server | `8.8.8.8` |
| Domain | `noc.lab` |
| Excluded Addresses | `10.10.10.1 - 10.10.10.20` |

STORE-PC1 was configured as a DHCP client and received `10.10.10.21/24` from RTR-01 during Phase 1 validation.

---

### 🖥️ Management Network

Network-device management traffic was separated from the user network using VLAN 20.

SW-01 was assigned the management SVI:

`10.10.20.2/24`

RTR-01 provides the VLAN 20 default gateway:

`10.10.20.1/24`

This provides SW-01 with a dedicated management address while allowing authorized management traffic to be routed between VLANs.

---

### 🔐 SSH & Baseline Device Hardening

RTR-01 and SW-01 were configured for remote administration using SSH.

Baseline configuration included:

- Device hostnames
- Local administrative authentication
- SSH version 2
- RSA keys
- VTY remote access
- Interface descriptions
- Unused switch interfaces assigned to VLAN 99
- Unused switch interfaces administratively disabled

Sensitive authentication information is intentionally excluded from the repository.

📄 [View RTR-01 Phase 1 Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/Phase%201%20Running-Config)

📄 [View SW-01 Phase 1 Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/SW-01%20-%20Switch/Running-Config)

---

## 4. Verification 🔎

After implementation, each major component of the internal network was verified to establish a known-good operational baseline.

### 🔀 VLAN & Access-Port State

SW-01 was checked using:

```text
show vlan brief
```

The output verifies:

- VLAN 10 — USERS exists
- VLAN 20 — MANAGEMENT exists
- VLAN 99 — PARKING-NATIVE exists
- STORE-PC1 access interface `Gi1/0/10` is assigned to VLAN 10
- Unused access interfaces are assigned to VLAN 99

<!-- INSERT SW-01 SHOW VLAN BRIEF SCREENSHOT -->

---

### 🔗 Trunk State

The trunk between SW-01 and RTR-01 was checked using:

```text
show interfaces trunk
```

The output verifies:

- `Gi1/0/1` is operating as an 802.1Q trunk
- Native VLAN is 99
- VLANs 10, 20, and 99 are allowed across the trunk

<!-- INSERT SW-01 SHOW INTERFACES TRUNK SCREENSHOT -->

---

### 🌐 Router Subinterface State

RTR-01 was checked using:

```text
show ip interface brief
```

The output verifies the operational state of the router-on-a-stick interfaces used by the internal network.

The verification includes:

- `Gi0/1.10` — VLAN 10
- `Gi0/1.20` — VLAN 20
- `Gi0/1.99` — Native VLAN 99

<!-- INSERT RTR-01 SHOW IP INTERFACE BRIEF SCREENSHOT -->

---

### 📡 DHCP Server State

RTR-01's DHCP binding table was checked using:

```text
show ip dhcp binding
```

The binding verifies that STORE-PC1 received an address from the VLAN 10 DHCP pool.

<!-- INSERT RTR-01 SHOW IP DHCP BINDING SCREENSHOT -->

---

### 💻 DHCP & Endpoint Verification

STORE-PC1 successfully received its network configuration from RTR-01 through DHCP.

The workstation received:

- IP address: `10.10.10.21/24`
- Default gateway: `10.10.10.1`
- DNS suffix: `noc.lab`

<img width="1906" height="227" alt="STORE-PC1 DHCP Configuration" src="https://github.com/user-attachments/assets/a7c243a1-8449-4377-89f0-4867aed5be59" />

This verifies the DHCP configuration from the endpoint side of the connection.

---

### 🔬 DHCP Packet Capture

Wireshark was used while STORE-PC1 requested a DHCP lease.

The capture shows the complete DHCP DORA process:

```text
Discover → Offer → Request → ACK
```

<img width="1918" height="1031" alt="STORE-PC1 DHCP DORA Packet Capture" src="https://github.com/user-attachments/assets/bdcfbd58-fe82-497e-aaa6-b91d7b4ec3f7" />

The packet capture provides protocol-level evidence that STORE-PC1 successfully obtained its network configuration from RTR-01.

---

### 📡 Connectivity Verification

Connectivity was tested from STORE-PC1 to both the local VLAN 10 gateway and the SW-01 management interface in VLAN 20.

#### STORE-PC1 → VLAN 10 Gateway

Target:

```text
10.10.10.1
```

<img width="1915" height="399" alt="STORE-PC1 to VLAN 10 Gateway Ping" src="https://github.com/user-attachments/assets/26d811b7-7e61-437a-9b3d-308381903bda" />

Successful communication with `10.10.10.1` verifies local connectivity between STORE-PC1 and its default gateway.

#### STORE-PC1 → SW-01 Management Interface

Target:

```text
10.10.20.2
```

<!-- REPLACE WITH CLEAN SUCCESSFUL PING SCREENSHOT -->

Successful communication with `10.10.20.2` verifies that traffic from STORE-PC1 can leave VLAN 10, be routed through RTR-01, and reach the MANAGEMENT network in VLAN 20.

---

### 🔐 SSH Verification

Remote administration was tested from STORE-PC1 to both physical Cisco devices.

#### RTR-01 SSH Verification

<!-- INSERT CLEAN RTR-01 SSH LOGIN SCREENSHOT -->

#### SW-01 SSH Verification

<!-- INSERT CLEAN SW-01 SSH LOGIN SCREENSHOT -->

Successful authentication verifies remote administrative access to both network devices.

---

### 🔒 Unused-Port State

Unused switch interfaces were assigned to VLAN 99 and administratively disabled.

The VLAN assignment is visible through:

```text
show vlan brief
```

The administrative state of the interfaces can be verified using the appropriate interface-status output.

<!-- INSERT UNUSED-PORT HARDENING VERIFICATION SCREENSHOT -->

This confirms that unused interfaces are isolated from the active user and management VLANs.

---

## 5. Troubleshooting 🛠️

### SSH Client Compatibility

During Phase 1 testing, STORE-PC1 could reach the Cisco devices over the network, but the Windows OpenSSH client initially could not establish an SSH session.

The issue was isolated to SSH compatibility rather than basic IP connectivity.

The older Cisco IOS versions supported legacy SSH cryptographic algorithms that were not enabled by default by the newer Windows SSH client.

After adjusting the client-side SSH compatibility settings, STORE-PC1 successfully established SSH sessions with both RTR-01 and SW-01.

This troubleshooting process helped distinguish between two different failure domains:

```text
Network connectivity → Working
SSH application session → Initially failing
```

The underlying network did not require redesign. The issue existed at the SSH client/server compatibility layer.

---

## 6. Phase 1 Results 🏁

Phase 1 established and validated a known-good internal branch network with:

- VLAN-based user and management segmentation
- 802.1Q trunking
- Router-on-a-stick inter-VLAN routing
- Automated endpoint addressing through DHCP
- Dedicated network-device management addressing
- SSH remote administration
- Disabled and isolated unused switch interfaces
- Verified DHCP operation
- Verified endpoint connectivity
- Verified inter-VLAN communication
- A documented baseline for later troubleshooting

The completed internal network provides the foundation for the remaining NOC Operations Simulation phases.

Phase 2 extends the branch beyond the internal network by connecting RTR-01 to the upstream AT&T gateway and implementing NAT/PAT for Internet access.

🌐 **Next Phase:** [02 | Upstream Connectivity & NAT](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Upstream%20Connectivity%20&%20NAT.md)
