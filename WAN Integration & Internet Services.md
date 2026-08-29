# 02 | WAN & Internet Connectivity 🌐

Extending the internal NOC lab to the Internet while maintaining the segmented network established during Phase 1.

---

## 1. Purpose & Objectives 🎯

The purpose of this phase is to provide Internet connectivity to the NOC Operations Simulation by connecting RTR-01 to the existing home gateway and implementing outbound NAT/PAT.

| Design Goal | Purpose |
| --- | --- |
| WAN connectivity | Connect RTR-01 to the upstream Internet gateway |
| Default routing | Provide a path from the lab toward external networks |
| NAT/PAT | Translate private lab addresses for Internet access |
| Preserve segmentation | Maintain the existing USERS and MANAGEMENT networks |
| Internet validation | Verify end-to-end connectivity from the lab to the Internet |

---

## 2. Network Design 🗺️

Phase 2 extends the existing internal network by connecting RTR-01 to the upstream AT&T gateway.

### Physical Path

**STORE-PC1 → SW-01 → RTR-01 → AT&T Gateway → Internet**

The existing Phase 1 LAN design remains unchanged. `GigabitEthernet0/0` on RTR-01 is introduced as the WAN-facing interface.

### WAN Addressing

| Device / Interface | Address | Purpose |
| --- | --- | --- |
| RTR-01 Gi0/0 | `192.168.1.86/24` | WAN interface |
| AT&T Gateway | `192.168.1.254` | Upstream gateway |
| VLAN 10 — USERS | `10.10.10.0/24` | User network |
| VLAN 20 — MANAGEMENT | `10.10.20.0/24` | Management network |

RTR-01 receives its WAN address through DHCP from the AT&T gateway.

A default route learned through the WAN connection provides a path from RTR-01 toward external networks.

---

## 3. WAN Implementation ⚙️

RTR-01 was connected to the AT&T gateway to provide upstream Internet connectivity for the lab.

### 🌐 RTR-01 — WAN Interface

`GigabitEthernet0/0` was configured as the WAN-facing interface.

- Interface: `Gi0/0`
- Addressing: DHCP
- Assigned address: `192.168.1.86/24`
- Upstream gateway: `192.168.1.254`
- NAT role: Outside

The AT&T gateway automatically provided RTR-01 with its WAN address and a default route.

### Default Route

RTR-01 installed the following default route toward the AT&T gateway:

`0.0.0.0/0 → 192.168.1.254`

This route allows traffic destined for networks outside of the lab to be forwarded toward the Internet.

---

## 4. NAT/PAT Implementation 🔄

NAT/PAT was configured on RTR-01 to allow devices using private lab addresses to access external networks.

### NAT Interface Roles

| Interface | Network | NAT Role |
| --- | --- | --- |
| Gi0/1.10 | `10.10.10.0/24` | Inside |
| Gi0/1.20 | `10.10.20.0/24` | Inside |
| Gi0/0 | `192.168.1.0/24` | Outside |

A standard ACL identifies the internal networks eligible for translation:

- `10.10.10.0/24` — USERS
- `10.10.20.0/24` — MANAGEMENT

PAT overload uses the IP address assigned to `Gi0/0`, allowing multiple internal devices to share RTR-01's WAN address when accessing external networks.

### Translation Example

During validation, STORE-PC1 generated Internet traffic using its private address:

`10.10.10.21 → 192.168.1.86 → Internet`

RTR-01 translated the workstation's private address to its WAN address while maintaining individual sessions using TCP and UDP port numbers.

📄 [View RTR-01 NAT Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/NAT%20Configuration.md)

📄 [View RTR-01 Phase 2 Running-Config](../Configs/RTR-01%20-%20Router/Phase%202%20Running-Config)

---

## 5. Validation & Troubleshooting 🔎

Phase 2 connectivity was validated in stages to verify the WAN connection, routing, NAT/PAT, and DNS independently.

### 🌐 WAN Connectivity

RTR-01 successfully received `192.168.1.86/24` through DHCP on `Gi0/0`.

Connectivity from RTR-01 to the upstream AT&T gateway was verified:

`RTR-01 → 192.168.1.254` ✅

RTR-01 was then able to reach an external Internet address:

`RTR-01 → 8.8.8.8` ✅

### 🛣️ Default Route Verification

RTR-01 installed a default route using the AT&T gateway as the next hop:

`0.0.0.0/0 → 192.168.1.254`

This confirmed that traffic for unknown external networks would be forwarded toward the upstream gateway.

### 🔄 NAT/PAT Verification

NAT statistics confirmed that the configuration was actively translating traffic.

Verified NAT roles:

- Outside interface: `GigabitEthernet0/0`
- Inside interface: `GigabitEthernet0/1.10`
- Inside interface: `GigabitEthernet0/1.20`

During final validation, RTR-01 recorded thousands of successful NAT translation hits with no misses.

The translation table also confirmed STORE-PC1 traffic being translated from `10.10.10.21` to RTR-01's WAN address `192.168.1.86`.

### 💻 Endpoint Internet Verification

Internet connectivity was tested directly from STORE-PC1.

The workstation successfully reached:

`STORE-PC1 → 8.8.8.8` ✅

The test completed with 0% packet loss.

### 🔎 DNS Verification

STORE-PC1 successfully queried Google DNS at `8.8.8.8` and resolved `google.com`.

A final hostname connectivity test was successful:

`STORE-PC1 → google.com` ✅

This confirmed that routing, NAT/PAT, Internet connectivity, and DNS resolution were all functioning through the lab network.

---

## 6. Results & Handoff 🏁

The WAN & Internet Connectivity phase successfully extended the NOC Operations Simulation from an isolated internal network to an Internet-connected environment.

The completed environment now provides:

- Upstream WAN connectivity through RTR-01
- DHCP-based WAN addressing
- Default routing toward the AT&T gateway
- NAT/PAT for internal networks
- Internet access from STORE-PC1
- Working DNS resolution
- Preserved VLAN segmentation from Phase 1

The Phase 2 configuration was saved to NVRAM and documented as the new known-good network baseline.

This baseline will be used during the next phase to introduce centralized network visibility and monitoring.

📊 **Next Phase: Network Monitoring**
