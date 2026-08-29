# 02 | WAN & Internet Connectivity 🌐

Extending the internal NOC lab to the Internet while maintaining the segmented network established during Phase 1.

Phase 2 introduced upstream connectivity between RTR-01 and the AT&T home gateway. RTR-01 was configured to obtain its WAN address through DHCP and provide Internet access to the internal VLANs using NAT/PAT.

---

## 1. Purpose & Objectives 🎯

The goal of Phase 2 was to extend the known-good internal network from Phase 1 to an upstream network and establish working Internet connectivity.

### Objectives

- Connect RTR-01 to the upstream AT&T gateway
- Obtain a WAN IPv4 address through DHCP
- Establish a default route toward the upstream gateway
- Configure NAT/PAT for the internal networks
- Provide Internet access to STORE-PC1
- Verify DNS resolution
- Preserve the VLAN segmentation established during Phase 1

---

## 2. Network Design 🗺️

### Physical Path

`STORE-PC1 → SW-01 → RTR-01 → AT&T Gateway → Internet`

### Addressing

| Device / Network | Interface | Address | Purpose |
| --- | --- | --- | --- |
| RTR-01 | `Gi0/0` | `192.168.1.86/24` | WAN |
| AT&T Gateway | LAN | `192.168.1.254` | Upstream Gateway |
| VLAN 10 | `Gi0/1.10` | `10.10.10.1/24` | USERS Gateway |
| VLAN 20 | `Gi0/1.20` | `10.10.20.1/24` | MANAGEMENT Gateway |
| STORE-PC1 | Ethernet | `10.10.10.21/24` | User Workstation |

> RTR-01 receives its WAN address dynamically from the upstream gateway, so the `192.168.1.86` address may change.

---

## 3. WAN Implementation ⚙️

RTR-01 `GigabitEthernet0/0` was connected to a LAN port on the AT&T gateway and configured as the WAN-facing interface.

The interface received:

- WAN Address: `192.168.1.86/24`
- Upstream Gateway: `192.168.1.254`
- Address Assignment: DHCP
- Interface State: Up/Up

The DHCP-learned WAN configuration also installed a default route toward the AT&T gateway.

### WAN Interface Verification

The final interface state confirmed that the WAN interface and internal ROAS subinterfaces were operational.

![RTR-01 WAN Interface Verification](YOUR-SCREENSHOT-URL)

---

## 4. NAT/PAT Implementation 🔄

The internal lab uses private IPv4 addressing that is not directly routable to the Internet.

NAT/PAT was configured on RTR-01 so internal devices could share the IPv4 address assigned to `GigabitEthernet0/0`.

### NAT Roles

| Interface | Network | NAT Role |
| --- | --- | --- |
| `Gi0/1.10` | `10.10.10.0/24` | Inside |
| `Gi0/1.20` | `10.10.20.0/24` | Inside |
| `Gi0/0` | `192.168.1.0/24` | Outside |

Standard ACL 1 identifies VLAN 10 and VLAN 20 as networks eligible for translation.

PAT overload then translates internal sessions to the address assigned to RTR-01's WAN interface.

### Translation Example

`10.10.10.21 → 192.168.1.86 → Internet`

📄 [View RTR-01 NAT Configuration](../Configs/RTR-01%20-%20Router/NAT%20Configuration)

---

## 5. Validation & Troubleshooting 🔎

Phase 2 was validated from both RTR-01 and STORE-PC1 to confirm that connectivity worked across the complete path.

### Default Route Verification

RTR-01 successfully learned a default route through the upstream AT&T gateway:

`0.0.0.0/0 → 192.168.1.254`

![RTR-01 Default Route Verification](YOUR-SCREENSHOT-URL)

### NAT/PAT Translation Verification

Active translations confirmed that STORE-PC1 traffic was being translated from its inside-local address `10.10.10.21` to RTR-01's inside-global address `192.168.1.86`.

![RTR-01 NAT PAT Translation Verification](YOUR-SCREENSHOT-URL)

### Internet Connectivity Verification

STORE-PC1 successfully reached `8.8.8.8`, confirming end-to-end IPv4 connectivity through SW-01, RTR-01, the upstream gateway, and the Internet.

![STORE-PC1 Internet Connectivity Verification](YOUR-SCREENSHOT-URL)

### DNS Resolution Verification

A DNS lookup for `google.com` using `8.8.8.8` successfully returned records, confirming that DNS traffic was functioning through the NAT/PAT configuration.

![STORE-PC1 DNS Resolution Verification](YOUR-SCREENSHOT-URL)

### Verification Commands

```text
show ip interface brief
show ip route 0.0.0.0
show ip nat translations
show ip nat statistics
show access-list 1
```

Endpoint validation:

```text
ping 8.8.8.8
nslookup google.com
ping google.com
```

---

## 6. Results & Handoff 🏁

Phase 2 successfully extended the internal lab network to the Internet.

The completed environment now provides:

- Operational upstream WAN connectivity
- DHCP-based WAN addressing
- Default routing through the AT&T gateway
- NAT/PAT for VLAN 10 and VLAN 20
- Internet connectivity from STORE-PC1
- Functional DNS resolution
- Preserved internal VLAN segmentation
- A validated network baseline for monitoring

The Phase 2 configuration was saved as the new known-good baseline before moving into network monitoring.

📄 [View RTR-01 Phase 2 Running-Config](../Configs/RTR-01%20-%20Router/Phase%202%20Running-Config)

---

## Next Phase

### 03 | Network Monitoring 📊

Phase 3 will introduce centralized network monitoring to provide visibility into device availability, network health, and events across the physical lab environment.
