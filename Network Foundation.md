
# 01 | Network Foundation 🏗️

> Establishing and validating the known-good internal network that will serve as the foundation for monitoring and incident-response operations.

---

## 1. Purpose

The purpose of the Network Foundation phase is to deploy and validate the internal network for the NOC Operations Simulation.

Before monitoring, alerting, and incident-response workflows can be introduced, the underlying network must have a documented and verified known-good state.

This phase establishes that baseline using physical Cisco routing and switching equipment. The network is segmented using VLANs, routed through router-on-a-stick (ROAS), centrally addressed through DHCP, and remotely manageable through SSH.

Packet captures and command-line verification are used to confirm that the network is operating as designed.

The completed baseline will later provide a reference point when controlled failures are introduced during the Incident Response phase.

---

## 2. Phase Objectives

The objectives of this phase are to:

- Deploy the physical router, switch, and test endpoint.
- Establish the physical network topology.
- Create logical network segmentation using VLANs.
- Configure an IEEE 802.1Q trunk between the router and switch.
- Implement router-on-a-stick inter-VLAN routing.
- Configure a dedicated management VLAN.
- Provide dynamic IPv4 addressing to user endpoints with DHCP.
- Establish secure remote administration using SSH.
- Disable and isolate unused switch interfaces.
- Validate Layer 1, Layer 2, and Layer 3 connectivity.
- Capture and analyze DHCP, ARP, and ICMP traffic with Wireshark.
- Document a known-good network baseline before WAN connectivity and monitoring are introduced.

---

## 3. Equipment Used

The Network Foundation was built using physical Cisco networking equipment and a Windows workstation used for configuration, testing, and packet analysis.

| Equipment | Model / Platform | Role |
| --- | --- | --- |
| Router | Cisco ISR 2911 | Inter-VLAN routing, DHCP, and NAT |
| Switch | Cisco Catalyst 2960 | VLAN segmentation, 802.1Q trunking, and Layer 2 connectivity |
| Workstation | Windows PC | User endpoint, network testing, SSH access, and packet capture |
| Ethernet Cabling | Cat5e/Cat6 | Physical connectivity between network devices |

### Physical Topology

<img width="4280" height="3406" alt="Image" src="https://github.com/user-attachments/assets/d56cc878-b6e6-4a8e-9a93-3c87da51f04e" />

### Logical Topology

<img width="1292" height="277" alt="Image" src="https://github.com/user-attachments/assets/707745e1-d562-45d1-a87e-ad772241c088" />
```

WAN connectivity is intentionally excluded from this phase. `RTR-01 Gi0/0` is reserved for WAN integration during Phase 2.

---

## 5. Interface Mapping

| Device | Interface | Connection / Role |
| --- | --- | --- |
| RTR-01 | Gi0/1 | Trunk to SW-01 |
| RTR-01 | Gi0/1.10 | VLAN 10 gateway |
| RTR-01 | Gi0/1.20 | VLAN 20 gateway |
| RTR-01 | Gi0/1.99 | Native VLAN |
| SW-01 | Gi1/0/1 | Trunk to RTR-01 |
| SW-01 | Gi1/0/10 | STORE-PC1 |
| SW-01 | Vlan20 | Switch management |
| SW-01 | Remaining ports | Unused |

---

## 6. VLAN & IP Addressing Design

Three VLANs are used to separate network functions.

| VLAN | Name | Network | Gateway | Purpose |
| ---: | --- | --- | --- | --- |
| 10 | `USERS` | 10.10.10.0/24 | 10.10.10.1 | User endpoints |
| 20 | `MANAGEMENT` | 10.10.20.0/24 | 10.10.20.1 | Infrastructure management |
| 99 | `PARKING-NATIVE` | N/A | N/A | Native VLAN and unused-port isolation |

VLAN 20 provides separation between user traffic and infrastructure management.

VLAN 99 is intentionally not used for production endpoints. It serves as the native VLAN on the router-to-switch trunk and as the parking VLAN for unused switch interfaces.

---

## 7. Layer 2 Switching

The required VLANs were created on `SW-01`.

`Gi1/0/10` was configured as an access interface in VLAN 10 for `STORE-PC1`.

`Gi1/0/1` was configured as a static 802.1Q trunk toward `RTR-01`.

The trunk carries:

```text
10,20,30,99
```

with VLAN 99 configured as the native VLAN.

Unused switch interfaces were assigned to VLAN 99 and administratively shut down to prevent unintended network access.

### Trunk Verification

The completed trunk was validated with:

```text
SW-01# show interfaces trunk

Port        Mode             Encapsulation  Status        Native vlan
Gi1/0/1     on               802.1q         trunking      99

Port        Vlans allowed on trunk
Gi1/0/1     10,20,30,99

Port        Vlans allowed and active in management domain
Gi1/0/1     10,20,30,99

Port        Vlans in spanning tree forwarding state and not pruned
Gi1/0/1     10,20,30,99
```

This confirmed that `Gi1/0/1` was actively trunking and forwarding all four required VLANs.

---

## 8. Inter-VLAN Routing

`RTR-01` provides Layer 3 routing using router-on-a-stick.

The physical `Gi0/1` interface carries the trunk while individual subinterfaces provide the Layer 3 gateway for each routed VLAN.

```text
Gi0/1
 │
 ├── Gi0/1.10 → 10.10.10.1/24
 ├── Gi0/1.20 → 10.10.20.1/24
 ├── Gi0/1.30 → 10.10.30.1/24
 └── Gi0/1.99 → Native VLAN
```

The router configuration includes:

```text
interface GigabitEthernet0/1
 description ## 802.1Q TRUNK TO SW-01 Gi1/0/1 ##
 no ip address

interface GigabitEthernet0/1.10
 description ## VLAN 10 USERS GATEWAY ##
 encapsulation dot1Q 10
 ip address 10.10.10.1 255.255.255.0

interface GigabitEthernet0/1.20
 description ## VLAN 20 MANAGEMENT GATEWAY ##
 encapsulation dot1Q 20
 ip address 10.10.20.1 255.255.255.0

interface GigabitEthernet0/1.30
 description ## VLAN 30 SERVERS GATEWAY ##
 encapsulation dot1Q 30
 ip address 10.10.30.1 255.255.255.0

interface GigabitEthernet0/1.99
 description ## VLAN 99 NATIVE ##
 encapsulation dot1Q 99 native
```

The routing table confirmed that all three routed VLAN networks were directly connected:

```text
C  10.10.10.0/24 is directly connected, GigabitEthernet0/1.10
C  10.10.20.0/24 is directly connected, GigabitEthernet0/1.20
C  10.10.30.0/24 is directly connected, GigabitEthernet0/1.30
```

---

## 9. Switch Management

`SW-01` uses VLAN 20 as its management network.

The switch management SVI is:

```text
Vlan20
10.10.20.2/24
```

The corresponding router gateway is:

```text
RTR-01 Gi0/1.20
10.10.20.1/24
```

Bidirectional connectivity was successfully verified.

From `SW-01`:

```text
SW-01# ping 10.10.20.1

!!!!!
Success rate is 100 percent (5/5)
```

From `RTR-01`:

```text
RTR-01# ping 10.10.20.2

!!!!!
Success rate is 100 percent (5/5)
```

This established a working Layer 3 management path between the router and switch.

---

## 10. DHCP Services

`RTR-01` provides DHCP services for VLAN 10.

Addresses `10.10.10.1` through `10.10.10.20` are excluded from dynamic allocation.

```text
ip dhcp excluded-address 10.10.10.1 10.10.10.20

ip dhcp pool VLAN10-USERS
 network 10.10.10.0 255.255.255.0
 default-router 10.10.10.1
 dns-server 8.8.8.8
 domain-name noc.lab
```

`STORE-PC1` successfully received:

| Parameter | Assigned Value |
| --- | --- |
| IPv4 Address | 10.10.10.21 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 10.10.10.1 |
| DHCP Server | 10.10.10.1 |
| DNS Server | 8.8.8.8 |
| DNS Suffix | noc.lab |

The router DHCP binding table confirmed the active lease:

```text
RTR-01# show ip dhcp binding

IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name

10.10.10.21         016c.6e07.2109.1e       Aug 23 2026 04:30 PM    Automatic
```

This verified that DHCP was operating successfully across the VLAN 10 access path.

---

## 11. DHCP Packet Analysis

Wireshark was used on `STORE-PC1` to observe the DHCP address-allocation process.

The capture verified the four-message DHCP DORA exchange:

```text
DHCP Discover
      ↓
DHCP Offer
      ↓
DHCP Request
      ↓
DHCP ACK
```

This demonstrated the complete process used by `STORE-PC1` to dynamically obtain its IPv4 configuration from `RTR-01`.

### DHCP DORA Capture

<!-- Replace YOUR-IMGUR-LINK with your direct Imgur URL -->

<img src="YOUR-IMGUR-LINK" alt="Wireshark DHCP DORA Capture"/>

The packet capture provides wire-level evidence of the same DHCP process confirmed through the endpoint configuration and Cisco DHCP binding table.

---

## 12. ARP & ICMP Packet Analysis

Wireshark was also used to examine ARP and ICMP behavior.

### ARP Analysis

ARP was observed while `STORE-PC1` resolved the MAC address associated with its default gateway.

The process demonstrates how a host prepares traffic for a destination outside its local subnet:

```text
Destination IP
      ↓
Routing Decision
      ↓
Default Gateway
      ↓
ARP Resolution
      ↓
Ethernet Frame
```

### ARP Capture

<!-- Replace YOUR-IMGUR-LINK with your direct Imgur URL -->

<img src="YOUR-IMGUR-LINK" alt="Wireshark ARP Capture"/>

### ICMP Analysis

ICMP traffic was captured while testing connectivity between VLAN 10 and the management network.

This provided packet-level verification that traffic was successfully being routed through `RTR-01`.

### ICMP Capture

<!-- Replace YOUR-IMGUR-LINK with your direct Imgur URL -->

<img src="YOUR-IMGUR-LINK" alt="Wireshark Inter-VLAN ICMP Capture"/>

---

## 13. SSH Remote Management

SSH version 2 was enabled to provide secure remote administration of the Cisco devices.

The management domain was standardized as:

```text
noc.lab
```

A local privileged administrative account was created, and VTY access was restricted to SSH.

Relevant configuration includes:

```text
ip domain name noc.lab
ip ssh version 2

line vty 0 4
 login local
 transport input ssh

line vty 5 15
 login local
 transport input ssh
```

Remote SSH access was successfully validated to both `RTR-01` and `SW-01`.

### Legacy SSH Compatibility

During testing, the modern Windows OpenSSH client successfully reached TCP port 22 on the Cisco router but rejected the older cryptographic algorithms offered by Cisco IOS.

The initial connection failed during key-exchange negotiation rather than because of a routing, VLAN, or authentication problem.

Compatible legacy SSH algorithms were explicitly permitted on the lab workstation, after which SSH access succeeded.

This demonstrated that SSH troubleshooting can involve multiple independent stages:

```text
IP Reachability
      ↓
TCP Port 22
      ↓
Key Exchange
      ↓
Host Key Negotiation
      ↓
MAC Negotiation
      ↓
Authentication
      ↓
CLI Access
```

The issue was documented as a compatibility consideration between a modern SSH client and legacy Cisco IOS.

---

## 14. Endpoint Connectivity Validation

`STORE-PC1` successfully received `10.10.10.21/24` from `RTR-01`.

Connectivity to the VLAN 10 default gateway was verified:

```text
C:\Users\Marcus>ping 10.10.10.1

Pinging 10.10.10.1 with 32 bytes of data:

Reply from 10.10.10.1: bytes=32 time<1ms TTL=255
Reply from 10.10.10.1: bytes=32 time<1ms TTL=255
Reply from 10.10.10.1: bytes=32 time<1ms TTL=255
Reply from 10.10.10.1: bytes=32 time<1ms TTL=255

Ping statistics for 10.10.10.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

Inter-VLAN communication was also successfully validated.

The traffic path from the user endpoint to the management network is:

```text
STORE-PC1
10.10.10.21
     │
     │ VLAN 10
     ▼
SW-01 Gi1/0/10
     │
     │ 802.1Q Trunk
     ▼
RTR-01 Gi0/1.10
10.10.10.1
     │
     │ Layer 3 Routing
     ▼
RTR-01 Gi0/1.20
10.10.20.1
     │
     │ VLAN 20
     ▼
SW-01
10.10.20.2
```

This confirmed successful communication across the internal Layer 2 and Layer 3 infrastructure.

---

## 15. Troubleshooting Encountered

Establishing the baseline produced real troubleshooting scenarios that were documented rather than removed from the project history.

### 15.1 Dual Network Paths

During testing, `STORE-PC1` had both Wi-Fi and wired Ethernet active.

The Windows routing table contained two default routes:

```text
0.0.0.0/0 → AT&T Wi-Fi gateway
0.0.0.0/0 → RTR-01
```

This created competing network paths and caused traffic intended for the physical lab to behave unexpectedly.

The routing table was inspected using:

```text
route print
```

and interface addressing was verified using:

```text
ipconfig /all
```

After Wi-Fi was disabled, the wired lab path operated correctly and inter-VLAN connectivity succeeded.

### Lesson Learned

A successful local ping does not guarantee that subsequent traffic will leave through the expected network interface.

When troubleshooting a multi-homed endpoint, the host routing table must be considered alongside the Cisco network configuration.

---

### 15.2 SSH Algorithm Negotiation

The first SSH connection attempt reached `RTR-01` but failed with:

```text
Unable to negotiate with 10.10.10.1 port 22:
no matching key exchange method found
```

After allowing a compatible key-exchange method and RSA host key, the connection progressed further but encountered another negotiation failure:

```text
no matching MAC found
```

The router offered legacy SHA-1-based MAC algorithms that the modern OpenSSH client did not enable by default.

After permitting the required compatibility algorithms, SSH authentication and CLI access succeeded.

### Lesson Learned

An SSH failure does not automatically indicate a routing, firewall, credential, or Cisco configuration problem.

The troubleshooting process must identify **which stage of the SSH connection is failing**.

---

## 16. Baseline Verification

The following checks were completed before declaring the internal network baseline operational.

| Validation | Expected Result | Result |
| --- | --- | --- |
| RTR-01 ↔ SW-01 physical link | Connected | PASS |
| Gi1/0/1 trunk | Trunking | PASS |
| Native VLAN | VLAN 99 | PASS |
| Allowed VLANs | 10,20,30,99 | PASS |
| VLAN 10 USERS | Active | PASS |
| VLAN 20 MANAGEMENT | Active | PASS |
| VLAN 30 SERVERS | Active | PASS |
| VLAN 99 PARKING-NATIVE | Active | PASS |
| STORE-PC1 access port | VLAN 10 / Connected | PASS |
| SW-01 management SVI | 10.10.20.2 reachable | PASS |
| Inter-VLAN routing | Operational | PASS |
| DHCP | Lease successfully assigned | PASS |
| STORE-PC1 gateway | 10.10.10.1 reachable | PASS |
| DHCP DORA | Captured | PASS |
| ARP | Captured and analyzed | PASS |
| ICMP | Captured and analyzed | PASS |
| RTR-01 SSH | Successful | PASS |
| SW-01 SSH | Successful | PASS |
| Unused switch interfaces | VLAN 99 / Shutdown | PASS |

---

## 17. Known-Good Baseline

At the completion of this phase, the internal LBR-001 network has a documented known-good state.

```text
Physical Connectivity       PASS
VLAN Segmentation           PASS
802.1Q Trunking             PASS
Inter-VLAN Routing          PASS
Management Connectivity     PASS
DHCP                        PASS
Endpoint Connectivity       PASS
SSH Administration          PASS
Packet-Level Validation     PASS
Unused-Port Isolation       PASS
```

This baseline is important because future incident-response exercises require a known working state for comparison.

When controlled failures are introduced later in the project, troubleshooting can compare the incident condition against the configuration and behavior documented during this phase.

---

## 18. Handoff to Phase 2

The internal LBR-001 network is now ready to be extended beyond the local environment.

Phase 2 — **WAN & Internet Connectivity** will use the currently reserved `RTR-01 Gi0/0` interface to connect the lab to the upstream AT&T network.

The next phase will introduce:

- WAN connectivity
- Upstream IPv4 addressing
- Default routing
- NAT/PAT
- Internet access
- DNS validation
- Double-NAT documentation
- End-to-end WAN testing

WAN functionality will not be considered complete until internal VLAN traffic can successfully reach external destinations and the resulting traffic path has been validated.

---

## Phase Status

**Network Foundation: COMPLETE ✅**

**Next:** [02 | WAN & Internet Connectivity 🌐](02-WAN-Internet-Connectivity.md)

