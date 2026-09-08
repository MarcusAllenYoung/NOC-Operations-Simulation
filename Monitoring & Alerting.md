# 01 | Network Deployment 🏗️

Phase 1 establishes the internal branch network that serves as the foundation for the NOC Operations Simulation.

The goal of this phase is to create and validate a known-good internal network before introducing upstream Internet connectivity, centralized monitoring, alerting, and simulated incidents in later phases.

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

### ⛓️ Physical Topology

<!-- INSERT UPDATED PHASE 1 PHYSICAL TOPOLOGY -->

### 🔀 Logical Topology

```text
                    RTR-01
                       |
                    Gi0/1
                       |
                802.1Q Trunk
             VLAN 10, 20, 99
                       |
                    SW-01
                       |
                   Gi1/0/10
                       |
                  STORE-PC1
                   MacBook
```

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
| SW-01 | Unused interfaces | VLAN 99 / administratively disabled |

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
| STORE-PC1 | `10.10.10.x/24` | DHCP |

---

## 3. Network Implementation ⚙️

### 🔀 VLAN Configuration

Three VLANs were created on SW-01:

- VLAN 10 — USERS
- VLAN 20 — MANAGEMENT
- VLAN 99 — PARKING-NATIVE

`Gi1/0/10` was configured as a VLAN 10 access interface for STORE-PC1.

Unused switch interfaces were moved into VLAN 99 and administratively disabled.

#### SW-01

```text
[INSERT RELEVANT VLAN / ACCESS-PORT CONFIGURATION]
```

---

### 🔗 802.1Q Trunk Configuration

The connection between SW-01 and RTR-01 was configured as an 802.1Q trunk.

The trunk carries:

- VLAN 10 — USERS
- VLAN 20 — MANAGEMENT
- VLAN 99 — Native

#### SW-01

```text
[INSERT RELEVANT TRUNK CONFIGURATION]
```

The trunk provides the Layer 2 path required for RTR-01 to provide routing services for multiple VLANs over a single physical connection.

---

### 🌐 Router-on-a-Stick Configuration

RTR-01 was configured with 802.1Q subinterfaces to provide Layer 3 gateways for the internal VLANs.

| Subinterface | VLAN | Address |
| --- | ---: | --- |
| Gi0/1.10 | 10 | `10.10.10.1/24` |
| Gi0/1.20 | 20 | `10.10.20.1/24` |
| Gi0/1.99 | 99 | Native VLAN |

#### RTR-01

```text
[INSERT RELEVANT ROUTER-ON-A-STICK CONFIGURATION]
```

This allows traffic to be routed between the USERS and MANAGEMENT networks.

---

### 📡 DHCP Configuration

RTR-01 provides DHCP service for VLAN 10.

Infrastructure addresses were excluded from the DHCP pool before dynamic addresses were made available to user endpoints.

| Setting | Value |
| --- | --- |
| Network | `10.10.10.0/24` |
| Default Gateway | `10.10.10.1` |
| DNS Server | `8.8.8.8` |
| Domain | `noc.lab` |
| Excluded Addresses | `10.10.10.1 - 10.10.10.20` |

#### RTR-01

```text
[INSERT RELEVANT DHCP CONFIGURATION]
```

STORE-PC1 is configured as a DHCP client and receives its VLAN 10 network configuration from RTR-01.

---

### 🖥️ Management Network

SW-01 is remotely managed through VLAN 20.

The switch management SVI was configured as:

```text
10.10.20.2/24
```

with the following default gateway:

```text
10.10.20.1
```

This provides SW-01 with a dedicated management address separate from the VLAN 10 user network.

---

### 🔐 SSH & Baseline Device Hardening

RTR-01 and SW-01 were configured for remote SSH administration.

Baseline device configuration included:

- Device hostnames
- Local administrative account
- SSH version 2
- RSA keys
- VTY remote access
- Interface descriptions
- Unused interfaces disabled
- Unused switch interfaces placed in VLAN 99

Sensitive authentication information is intentionally excluded from this repository.

📄 [View RTR-01 Phase 1 Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/Phase%201%20Running-Config)

📄 [View SW-01 Phase 1 Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/SW-01%20-%20Switch/Running-Config)

---

## 4. Verification 🔎

After configuration, each major component of the internal network was verified to establish a known-good operational baseline.

### 🔀 VLAN & Access-Port State

`show vlan brief` on SW-01 confirms that the required VLANs exist and that the appropriate access interfaces are assigned.

```text
show vlan brief
```

<!-- INSERT NEW SW-01 VLAN SCREENSHOT -->

Verified state:

- VLAN 10 — USERS
- VLAN 20 — MANAGEMENT
- VLAN 99 — PARKING-NATIVE
- `Gi1/0/10` assigned to VLAN 10

---

### 🔗 Trunk State

`show interfaces trunk` confirms that the link between SW-01 and RTR-01 is operating as an 802.1Q trunk.

```text
show interfaces trunk
```

<!-- INSERT NEW TRUNK SCREENSHOT -->

The verification confirms:

- `Gi1/0/1` is operating as a trunk
- Native VLAN is 99
- VLANs 10, 20, and 99 are permitted across the trunk

---

### 🌐 Router Subinterface State

`show ip interface brief` on RTR-01 confirms that the router-on-a-stick subinterfaces are operational.

```text
show ip interface brief
```

<!-- INSERT NEW RTR-01 INTERFACE SCREENSHOT -->

The verification confirms that the VLAN gateway subinterfaces are operational.

---

### 📡 DHCP Server State

RTR-01's DHCP binding table confirms that STORE-PC1 received an address from the VLAN 10 DHCP pool.

```text
show ip dhcp binding
```

<!-- INSERT NEW DHCP BINDING SCREENSHOT -->

---

### 💻 STORE-PC1 DHCP State

STORE-PC1 is configured as a DHCP client.

<!-- INSERT NEW MACBOOK DHCP SCREENSHOT -->

The endpoint receives:

- IP address from `10.10.10.0/24`
- Default gateway `10.10.10.1`
- Network configuration supplied by RTR-01

---

### 🔬 DHCP Packet Capture

A packet capture was performed while STORE-PC1 requested its network configuration.

The capture verifies the complete DHCP DORA process:

```text
Discover → Offer → Request → ACK
```

<!-- INSERT NEW MACBOOK WIRESHARK DHCP CAPTURE -->

This provides packet-level verification that STORE-PC1 received its configuration through DHCP rather than simply showing the final assigned address.

---

### 📡 Connectivity Verification

Connectivity from STORE-PC1 was tested against both its local gateway and the separate MANAGEMENT network.

#### STORE-PC1 → VLAN 10 Gateway

```text
STORE-PC1 → 10.10.10.1
```

<!-- INSERT NEW SUCCESSFUL PING SCREENSHOT -->

#### STORE-PC1 → SW-01 Management Interface

```text
STORE-PC1 → 10.10.20.2
```

<!-- INSERT NEW SUCCESSFUL PING SCREENSHOT -->

Successful communication with `10.10.20.2` verifies that traffic can leave VLAN 10, be routed by RTR-01, and reach a device in VLAN 20.

---

### 🔐 SSH Verification

Remote SSH access was verified to both physical Cisco devices from STORE-PC1.

#### RTR-01

<!-- INSERT NEW RTR-01 SSH SCREENSHOT -->

#### SW-01

<!-- INSERT NEW SW-01 SSH SCREENSHOT -->

Successful authentication confirms that both devices can be remotely administered over the internal network.

---

## 5. Troubleshooting 🛠️

### SSH Client Compatibility

During initial testing, the modern SSH client used by the original workstation could reach the Cisco devices but could not initially establish an SSH session.

The issue was traced to compatibility between the modern SSH client and the legacy cryptographic algorithms supported by the older Cisco IOS versions.

After adjusting the client-side SSH compatibility settings, remote administration was successfully verified.

This was an SSH compatibility problem rather than an IP-connectivity failure.

---

## 6. Phase 1 Results 🏁

Phase 1 established a known-good internal branch network with:

- VLAN-based user and management segmentation
- 802.1Q trunking
- Router-on-a-stick inter-VLAN routing
- Automated endpoint addressing through DHCP
- Dedicated switch management addressing
- SSH remote administration
- Disabled and isolated unused switch interfaces
- Verified endpoint and inter-VLAN connectivity

This baseline provides the internal network that the remaining phases build upon.

Phase 2 extends the branch to the upstream network and Internet using WAN connectivity and NAT/PAT.

🌐 **Next Phase:** [02 | Upstream Connectivity & NAT](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Upstream%20Connectivity%20&%20NAT.md)
