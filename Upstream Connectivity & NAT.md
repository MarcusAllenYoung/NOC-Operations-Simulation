# 02 | Upstream Connectivity & NAT 🌐

Connecting the retail store branch to the upstream network and providing Internet access.

## 1. Purpose 🎯

The purpose of this phase was to extend the retail store branch network established in Phase 1 by connecting RTR-01 to an upstream network and providing Internet access.

RTR-01 was connected to the AT&T gateway and configured to receive an upstream IP address through DHCP. NAT/PAT was configured to allow devices using private IP addresses inside the branch to access the Internet.

### 🛠️ Equipment Used

| **Equipment** | **Model / Platform** | **Role** |
| --- | --- | --- |
| Router | Cisco ISR 2911 | Routing, NAT/PAT, and upstream connectivity |
| Switch | Cisco Catalyst 2960 | VLANs, trunking, and Layer 2 connectivity |
| Workstation | Windows PC | User endpoint, SSH, Connectivity testing |
| Upstream Gateway | AT&T Gateway | Upstream network and Internet access |
| Cabling | Cat5e/Cat6 | Physical network connectivity |

### ⛓️ Physical Topology

Phase 2 adds the WAN connection between RTR-01 and the AT&T gateway.

<img width="4032" height="2744" alt="Image" src="https://github.com/user-attachments/assets/57754db1-ffe5-4331-87be-10cf94659a4f" />

### 🔀 Logical Topology
<img width="926" height="377" alt="Image" src="https://github.com/user-attachments/assets/b4c60271-36e5-4914-bcc2-e1b0a28db1ce" />

### 🔗 Objective

- Connect RTR-01 to the upstream AT&T gateway
- Configure the upstream interface to receive an IP address through DHCP
- Establish a default route toward the upstream gateway
- Configure NAT/PAT for the internal branch networks
- Provide Internet access to STORE-PC1
- Verify external connectivity and DNS resolution

### 🖥️ Device Reference

| **Device Name** | **Device** | **Location** |
| --- | --- | --- |
| **RTR-01** | Cisco ISR 2911 Router | Retail Store Branch |
| **SW-01** | Cisco Catalyst 2960X Switch | Retail Store Branch |
| **STORE-PC1** | Windows PC | Retail Store Branch |
| **AT&T Gateway** | Upstream Gateway | ISP / Upstream Network |

### 🔌 Interface Mapping

| **Device** | **Interface** | **Role** |
| --- | --- | --- |
| RTR-01 | Gi0/0 | Upstream connection to AT&T gateway |
| RTR-01 | Gi0/1 | Link to SW-01 |
| RTR-01 | Gi0/1.10 | VLAN 10 gateway subinterface / NAT inside |
| RTR-01 | Gi0/1.20 | VLAN 20 gateway subinterface / NAT inside |
| RTR-01 | Gi0/1.99 | Native VLAN Subinterface |
| SW-01 | Gi1/0/1 | Trunk link to RTR-01 |
| SW-01 | Gi1/0/10 | Access link to STORE-PC1 |
| SW-01 | Int Vlan 20 | SVI Interface |
| SW-01 | Remaining ports | Unused / shutdown |

### 🌐 WAN & IP Addressing

| Host / Interface | Address | Purpose |
| --- | --- | --- |
| VLAN 10 | 10.10.10.0/24 | USERS network |
| VLAN 20 | 10.10.20.0/24 | MANAGEMENT network |
| STORE-PC1 | 10.10.10.21/24 | End User |
| RTR-01 Gi0/0 | 192.168.1.86/24 | WAN interface |
| AT&T Gateway | 192.168.1.254 | Upstream gateway |

RTR-01 receives its WAN address dynamically through DHCP from the AT&T gateway, so the WAN address may change.

---

## 3. Network Configurations ⚙️

Phase 2 extended RTR-01 to the upstream network while preserving the internal VLAN, routing, DHCP, and management configuration established during Phase 1.

### 🌐 RTR-01 (WAN Connectivity)

`GigabitEthernet0/0` was connected to the AT&T gateway and configured as the WAN-facing interface.

- Interface g0/0 dynamically obtained its WAN IP address from the AT&T gateway using DHCP.
- WAN address received: `192.168.1.86/24`
- Upstream gateway: `192.168.1.254`
- Default route learned through the WAN DHCP configuration

### 🔄 RTR-01 (NAT/PAT)

NAT/PAT was configured to provide Internet access to the private internal networks.

- VLAN 10 (`10.10.10.0/24`) configured for NAT
- VLAN 20 (`10.10.20.0/24`) configured for NAT
- `Gi0/1.10` and `Gi0/1.20` configured as NAT inside interfaces
- `Gi0/0` configured as the NAT outside interface
- Standard ACL used to identify traffic eligible for translation
- PAT overload used to share the RTR-01 WAN address
  
##### View RTR-01 Phase 2 Configurations below
- [Gi0/0 WAN-Facing interface Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/WAN%20Interface%20Configuration%20(RTR-01).md)
- [NAT Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/NAT%20Configuration.md)



---

## 4. Validation & Troubleshooting 🔎

### 🌐 WAN Interface Verification

RTR-01 successfully received its WAN address through DHCP from the upstream AT&T gateway.

The router received:

- WAN address: 192.168.1.86/24
- Upstream gateway: 192.168.1.254
- Interface status: up/up

<img width="957" height="126" alt="Image" src="https://github.com/user-attachments/assets/844b58aa-dba1-4c81-9cb5-51d659c888d8" />

---

### 🛣️ Default Route Verification

RTR-01 successfully installed a default route pointing toward the AT&T gateway.

`0.0.0.0/0 → 192.168.1.254`

This provides a path for traffic destined for networks outside of the internal lab.

<img width="958" height="65" alt="Image" src="https://github.com/user-attachments/assets/f600b4e0-f0c3-464d-9181-bb7713d1d21f" />

### 🔄 NAT/PAT Verification

NAT translations were verified while STORE-PC1 generated Internet traffic.

The translation table confirmed that STORE-PC1's private address `10.10.10.21` was translated to RTR-01's inside global address `192.168.1.86`.

**STORE-PC1 NAT Translation**

`10.10.10.21 → 192.168.1.86`

<img width="959" height="140" alt="Image" src="https://github.com/user-attachments/assets/90c5d765-a55f-4a8a-b05a-47d491acaaa8" />

> **Double NAT:** RTR-01 operates behind the AT&T gateway, so `192.168.1.86` is not a public Internet address. RTR-01 first translates internal VLAN traffic to its `192.168.1.86` WAN address, and the AT&T gateway performs a second NAT translation before the traffic reaches the Internet.

### 📡 Internet Connectivity Verification

End-to-end Internet connectivity was tested from STORE-PC1.

STORE-PC1 successfully reached Google's public DNS server at `8.8.8.8`, confirming connectivity through SW-01, RTR-01, the AT&T gateway, and the Internet.

**STORE-PC1 → Internet**

<img width="959" height="182" alt="Image" src="https://github.com/user-attachments/assets/ee7040c3-277d-4de8-b0b1-ae3976cd9115" />

### 🔎 DNS Resolution Verification

DNS resolution was tested from STORE-PC1 using `nslookup`.

The workstation successfully resolved `google.com` using the configured DNS server `8.8.8.8`, confirming that both Internet connectivity and DNS resolution were operational.

**STORE-PC1 DNS Verification**

<img width="959" height="236" alt="Image" src="https://github.com/user-attachments/assets/6b3554bc-5ef0-49c9-9dba-f5b13335109b" />

---

## 5. Results & Handoff 🏁

The WAN & Internet Connectivity phase successfully extended the internal network from Phase 1 to the Internet while maintaining the existing VLAN segmentation and internal routing design.

RTR-01 now provides upstream connectivity, default routing, and NAT/PAT services for the internal lab networks. STORE-PC1 successfully demonstrated end-to-end Internet connectivity and DNS resolution.

This known-good baseline will be used as a reference when network monitoring and centralized visibility are introduced during the next phase.

📊 Next Phase: [Monitoring & Alerting](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md)
