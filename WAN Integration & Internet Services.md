# 02 | WAN & Internet Connectivity 🌐

Extending the known-good internal network to the Internet while maintaining the segmentation established during Phase 1.

---

## 1. Purpose & Objectives 🎯

The purpose of this phase is to establish upstream WAN and Internet connectivity for the NOC Operations Simulation. This connectivity extends the internal network from Phase 1 and provides the foundation required for external connectivity and future monitoring operations.

| Design Goal | Purpose |
| --- | --- |
| WAN connectivity | Connect RTR-01 to the upstream AT&T gateway |
| DHCP WAN addressing | Dynamically obtain an address for the WAN interface |
| Default routing | Forward external traffic toward the upstream gateway |
| NAT/PAT | Translate private internal addresses for Internet access |
| Internet & DNS validation | Verify end-to-end external connectivity |
| Known-good baseline | Reference point for future monitoring and incident-response work |

---

## 2. Network Design 🗺️

### 🛠️ Equipment Used

| Equipment | Model / Platform | Role |
| --- | --- | --- |
| Router | Cisco ISR 2911 | Routing, DHCP, NAT/PAT, and WAN connectivity |
| Switch | Cisco Catalyst 2960 | VLANs, trunking, and Layer 2 connectivity |
| Workstation | Windows PC | User endpoint, testing, and verification |
| Upstream Gateway | AT&T Gateway | Internet access |
| Cabling | Cat5e/Cat6 | Physical network connectivity |

### ⛓️ Physical Topology


### 🔀 Logical Topology

The internal VLAN design from Phase 1 remains unchanged. Phase 2 adds the WAN connection between RTR-01 and the AT&T gateway.

<img width="876" height="130" alt="Image" src="https://github.com/user-attachments/assets/da7a796e-dd7a-4dea-ad33-84843306d83f" />

### 🔌 Interface Mapping

| Device | Interface | Role |
| --- | --- | --- |
| RTR-01 | Gi0/0 | WAN connection to AT&T gateway |
| RTR-01 | Gi0/1 | Trunk to SW-01 |
| RTR-01 | Gi0/1.10 | VLAN 10 gateway / NAT inside |
| RTR-01 | Gi0/1.20 | VLAN 20 gateway / NAT inside |
| RTR-01 | Gi0/1.99 | Native VLAN |
| SW-01 | Gi1/0/1 | Trunk to RTR-01 |
| SW-01 | Gi1/0/10 | STORE-PC1 |

### 🌐 WAN & IP Addressing

| Network / Device | Address | Purpose |
| --- | --- | --- |
| VLAN 10 | 10.10.10.0/24 | USERS network |
| VLAN 20 | 10.10.20.0/24 | MANAGEMENT network |
| STORE-PC1 | 10.10.10.21/24 | User workstation |
| RTR-01 Gi0/0 | 192.168.1.86/24 | WAN interface |
| AT&T Gateway | 192.168.1.254 | Upstream gateway |

RTR-01 receives its WAN address dynamically through DHCP from the AT&T gateway, so the WAN address may change.

---

## 3. Network Implementation ⚙️

Phase 2 extended RTR-01 to the upstream network while preserving the internal VLAN, routing, DHCP, and management configuration established during Phase 1.

### 🌐 RTR-01 — WAN Connectivity

`GigabitEthernet0/0` was connected to the AT&T gateway and configured as the WAN-facing interface.

- WAN addressing provided through DHCP
- WAN address received: `192.168.1.86/24`
- Upstream gateway: `192.168.1.254`
- Default route learned through the WAN DHCP configuration
- `Gi0/0` configured as the NAT outside interface

### 🔄 RTR-01 — NAT/PAT

NAT/PAT was configured to provide Internet access to the private internal networks.

- VLAN 10 (`10.10.10.0/24`) configured for NAT
- VLAN 20 (`10.10.20.0/24`) configured for NAT
- `Gi0/1.10` and `Gi0/1.20` configured as NAT inside interfaces
- `Gi0/0` configured as the NAT outside interface
- Standard ACL used to identify traffic eligible for translation
- PAT overload used to share the RTR-01 WAN address

Example translation path:

`10.10.10.21 → 192.168.1.86 → Internet`

📄 [View RTR-01 NAT Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/NAT%20Configuration)

📄 [View RTR-01 Phase 2 Configurations](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/Phase%202%20Running-Config)

---

## 4. Validation & Troubleshooting 🔎

### 🌐 WAN Interface Verification

RTR-01 successfully received its WAN address through DHCP from the upstream AT&T gateway.

The router received:

- WAN address: 192.168.1.86/24
- Upstream gateway: 192.168.1.254
- Interface status: up/up

<!-- Add WAN Interface Verification screenshot here -->

---

### 🛣️ Default Route Verification

RTR-01 successfully installed a default route pointing toward the AT&T gateway.

`0.0.0.0/0 → 192.168.1.254`

This provides a path for traffic destined for networks outside of the internal lab.

<!-- Add Default Route Verification screenshot here -->

### 🔄 NAT/PAT Verification

NAT translations were verified while STORE-PC1 generated Internet traffic.

The translation table confirmed that STORE-PC1's private address `10.10.10.21` was translated to the WAN address `192.168.1.86`.

**STORE-PC1 NAT Translation**

`10.10.10.21 → 192.168.1.86`

<!-- Add NAT/PAT Translation Verification screenshot here -->

### 📡 Internet Connectivity Verification

End-to-end Internet connectivity was tested from STORE-PC1.

STORE-PC1 successfully reached Google's public DNS server at `8.8.8.8`, confirming connectivity through SW-01, RTR-01, the AT&T gateway, and the Internet.

**STORE-PC1 → Internet**

<!-- Add STORE-PC1 Ping 8.8.8.8 screenshot here -->

### 🔎 DNS Resolution Verification

DNS resolution was tested from STORE-PC1 using `nslookup`.

The workstation successfully resolved `google.com` using the configured DNS server `8.8.8.8`, confirming that both Internet connectivity and DNS resolution were operational.

**STORE-PC1 DNS Verification**

<!-- Add STORE-PC1 nslookup google.com screenshot here -->

---

## 5. Results & Handoff 🏁

The WAN & Internet Connectivity phase successfully extended the internal network from Phase 1 to the Internet while maintaining the existing VLAN segmentation and internal routing design.

RTR-01 now provides upstream connectivity, default routing, and NAT/PAT services for the internal lab networks. STORE-PC1 successfully demonstrated end-to-end Internet connectivity and DNS resolution.

This known-good baseline will be used as a reference when network monitoring and centralized visibility are introduced during the next phase.

📊 Next Phase: Network Monitoring
