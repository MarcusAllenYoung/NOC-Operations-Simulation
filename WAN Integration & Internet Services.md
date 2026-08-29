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
