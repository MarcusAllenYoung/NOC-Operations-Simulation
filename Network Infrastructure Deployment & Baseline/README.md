# Network Infrastructure Deployment

## NOC Operations Simulation

**Status:** In Progress  
**Environment:** Physical Home Lab  
**Primary Technologies:** Cisco IOS, VLANs, 802.1Q, Router-on-a-Stick, DHCP, SSH, Wireshark  
**Devices:** Cisco 2911 Router, Cisco Catalyst Switch, Windows Test Endpoint

---

## 1. Overview

This phase establishes the baseline network infrastructure for the LBR-001 NOC Operations Simulation project.

The objective is to build and validate a small multi-VLAN network using physical Cisco equipment before introducing WAN connectivity, monitoring, observability, and simulated NOC incidents in later phases.

The network uses a Cisco router and Layer 2 switch with Router-on-a-Stick (ROAS) to provide inter-VLAN routing. A Windows workstation is currently used as the test endpoint for DHCP, routing, SSH, ARP, ICMP, and packet-analysis validation.

This phase focuses on proving that the underlying Layer 1, Layer 2, and Layer 3 infrastructure functions correctly before additional services are introduced.

---

## 2. Phase Objectives

The objectives of this phase are to:

- Establish physical connectivity between the router, switch, and endpoint.
- Create VLAN segmentation for users, management, servers, and unused ports.
- Configure an 802.1Q trunk between the switch and router.
- Implement Router-on-a-Stick for inter-VLAN routing.
- Configure a dedicated management SVI on the switch.
- Configure DHCP services for the user VLAN.
- Secure unused switch interfaces.
- Enable SSH-based remote management.
- Verify DHCP operation using Wireshark.
- Observe ARP behavior at Layer 2.
- Verify ICMP and inter-VLAN routing.
- Establish a documented and validated baseline before WAN integration.

---

## 3. Physical Topology

```text
                         RTR-01
                      Cisco 2911
                           |
                           | Gi0/1
                           |
                    802.1Q Trunk
                 VLANs 10,20,30,99
                           |
                           | Gi1/0/1
                        SW-01
                   Cisco Catalyst
                           |
                           | Gi1/0/10
                           | Access VLAN 10
                           |
                       STORE-PC
                  Windows Test Endpoint
```

The router currently provides the Layer 3 gateways for the internal VLANs. Internet/WAN connectivity is intentionally excluded from this phase.

---

## 4. Device Roles

| Device | Role | Function |
|---|---|---|
| RTR-01 | Router / Default Gateway | Inter-VLAN routing, DHCP and SSH management |
| SW-01 | Layer 2 Access Switch | VLAN segmentation, trunking and management |
| STORE-PC | User/Test Endpoint | DHCP, ICMP, SSH and packet-capture testing |

---

## 5. Interface Mapping

| Device | Interface | Connected To | Purpose |
|---|---|---|---|
| RTR-01 | Gi0/1 | SW-01 Gi1/0/1 | 802.1Q ROAS trunk |
| RTR-01 | Gi0/1.10 | VLAN 10 | USERS default gateway |
| RTR-01 | Gi0/1.20 | VLAN 20 | MANAGEMENT default gateway |
| RTR-01 | Gi0/1.30 | VLAN 30 | SERVERS default gateway |
| RTR-01 | Gi0/1.99 | VLAN 99 | Native VLAN |
| SW-01 | Gi1/0/1 | RTR-01 Gi0/1 | 802.1Q trunk |
| SW-01 | Gi1/0/10 | STORE-PC | VLAN 10 access port |
| SW-01 | VLAN 20 SVI | N/A | Switch management interface |

Unused switch interfaces are assigned to VLAN 99 and administratively shut down.

---

## 6. VLAN and IP Addressing Scheme

| VLAN | Name | Network | Gateway | Purpose |
|---:|---|---|---|---|
| 10 | USERS | 10.10.10.0/24 | 10.10.10.1 | User endpoints |
| 20 | MANAGEMENT | 10.10.20.0/24 | 10.10.20.1 | Network device management |
| 30 | SERVERS | 10.10.30.0/24 | 10.10.30.1 | Future server infrastructure |
| 99 | PARKING-NATIVE | N/A | N/A | Native VLAN and unused ports |

SW-01 uses:

`10.10.20.2/24`

as its management address on VLAN 20.

---

## 7. Router-on-a-Stick Design

Inter-VLAN routing is provided by RTR-01 using subinterfaces on physical interface `GigabitEthernet0/1`.

```text
GigabitEthernet0/1
        |
        +--- Gi0/1.10 ---- VLAN 10 ---- 10.10.10.1/24
        |
        +--- Gi0/1.20 ---- VLAN 20 ---- 10.10.20.1/24
        |
        +--- Gi0/1.30 ---- VLAN 30 ---- 10.10.30.1/24
        |
        +--- Gi0/1.99 ---- VLAN 99 ---- Native
```

Each VLAN receives a separate Layer 3 gateway while sharing one physical router interface.

The switch-to-router link permits VLANs:

```text
10,20,30,99
```

with VLAN 99 configured as the native VLAN.

### Routing Verification

RTR-01 successfully installed directly connected routes for all configured routed VLANs:

```text
C 10.10.10.0/24 is directly connected, GigabitEthernet0/1.10
C 10.10.20.0/24 is directly connected, GigabitEthernet0/1.20
C 10.10.30.0/24 is directly connected, GigabitEthernet0/1.30
```

This confirmed that the router subinterfaces were operational and available for inter-VLAN routing.

---

## 8. Switch Baseline

The switch was configured with four VLANs:

```text
VLAN 10 - USERS
VLAN 20 - MANAGEMENT
VLAN 30 - SERVERS
VLAN 99 - PARKING-NATIVE
```

`Gi1/0/1` operates as the trunk toward RTR-01.

Validation confirmed:

```text
Port        Mode   Encapsulation   Status    Native VLAN
Gi1/0/1     on     802.1q          trunking  99
```

The trunk permits:

```text
10,20,30,99
```

STORE-PC is connected to:

```text
Gi1/0/10
```

and the port operates as an access interface in VLAN 10.

All currently unused access interfaces were moved into VLAN 99 and administratively disabled.

This reduces the likelihood of an unused interface being connected to the production/user VLAN by default.

---

## 9. Management Network

SW-01 was assigned the management address:

```text
10.10.20.2/24
```

on the VLAN 20 SVI.

RTR-01 provides the VLAN 20 gateway:

```text
10.10.20.1/24
```

Bidirectional connectivity was successfully verified.

### SW-01 → RTR-01

```text
SW-01# ping 10.10.20.1

!!!!!
Success rate is 100 percent (5/5)
```

### RTR-01 → SW-01

```text
RTR-01# ping 10.10.20.2

!!!!!
Success rate is 100 percent (5/5)
```

This validated the VLAN 20 management path across the 802.1Q trunk.

---

## 10. DHCP Configuration and Validation

RTR-01 provides DHCP service for VLAN 10.

The first twenty addresses are excluded from dynamic allocation:

```text
10.10.10.1 - 10.10.10.20
```

The VLAN 10 DHCP scope uses:

| Parameter | Value |
|---|---|
| Network | 10.10.10.0/24 |
| Default Gateway | 10.10.10.1 |
| DNS Server | 8.8.8.8 |
| Domain | noc.lab |

STORE-PC successfully obtained:

```text
IPv4 Address:    10.10.10.21
Subnet Mask:     255.255.255.0
Default Gateway: 10.10.10.1
DHCP Server:     10.10.10.1
DNS Server:      8.8.8.8
DNS Suffix:      noc.lab
```

RTR-01 confirmed the lease:

```text
RTR-01# show ip dhcp binding

10.10.10.21    Automatic
```

The DHCP pool showed one active lease and no detected conflicts.

---

## 11. DHCP Packet Analysis

Wireshark was used on STORE-PC to observe the DHCP lease process.

A complete DHCP DORA exchange was captured:

```text
DHCP Discover
      ↓
DHCP Offer
      ↓
DHCP Request
      ↓
DHCP ACK
```

The capture demonstrated the complete process used by the endpoint to dynamically obtain its IPv4 configuration from RTR-01.

The client initially transmitted a broadcast DHCP Discover because it did not yet have an IPv4 address.

RTR-01 responded with an address offer.

The client then requested the offered configuration, and RTR-01 completed the exchange with a DHCP ACK.

This provided packet-level verification that DHCP was functioning rather than relying solely on the endpoint's assigned address.

---

## 12. ARP Packet Analysis

ARP traffic was captured between STORE-PC and RTR-01.

STORE-PC generated an ARP request similar to:

```text
Who has 10.10.10.1? Tell 10.10.10.21
```

RTR-01 responded with the MAC address associated with the VLAN 10 gateway.

This demonstrates how STORE-PC resolves the Layer 3 default-gateway address to a Layer 2 MAC address before Ethernet frames can be forwarded to RTR-01.

The ARP capture provides evidence that Layer 2 communication between STORE-PC, SW-01, and the router's VLAN 10 subinterface was functioning correctly.

---

## 13. Inter-VLAN Routing Verification

STORE-PC successfully reached its local default gateway:

```text
C:\> ping 10.10.10.1

Reply from 10.10.10.1
Reply from 10.10.10.1
Reply from 10.10.10.1
Reply from 10.10.10.1

0% packet loss
```

Inter-VLAN connectivity was then tested from:

```text
STORE-PC
10.10.10.21
VLAN 10
```

to:

```text
SW-01
10.10.20.2
VLAN 20
```

The test succeeded.

The packet path was:

```text
STORE-PC
10.10.10.21
     |
     | VLAN 10
     v
SW-01
     |
     | 802.1Q VLAN 10
     v
RTR-01 Gi0/1.10
10.10.10.1
     |
     | Layer 3 Routing
     v
RTR-01 Gi0/1.20
10.10.20.1
     |
     | 802.1Q VLAN 20
     v
SW-01 VLAN20
10.10.20.2
```

Wireshark captured both ICMP Echo Requests and Echo Replies between `10.10.10.21` and `10.10.20.2`, providing packet-level evidence of successful inter-VLAN routing.

---

## 14. SSH Management

SSH version 2 was enabled on the Cisco infrastructure using the local `nocadmin` account.

The lab domain is:

```text
noc.lab
```

Modern Windows OpenSSH initially rejected the older SSH cryptographic algorithms supported by the Cisco IOS version running on the physical hardware.

The initial connection failed because no mutually supported key-exchange algorithm could be negotiated.

After explicitly permitting the legacy algorithms required by the lab equipment, SSH connectivity was successfully established.

This was treated as a legacy-device compatibility issue rather than a routing or connectivity failure.

SSH access to both infrastructure devices was successfully validated.

> **Security note:** Legacy SSH algorithms are used only because of the age of the lab hardware/IOS software. They should not be considered appropriate cryptographic standards for a modern production deployment.

---

## 15. Troubleshooting — Dual Network Paths

One of the most important troubleshooting events during Phase 1 occurred when STORE-PC could successfully ping:

```text
10.10.10.1
```

but initially could not reach:

```text
10.10.20.1
```

The Cisco router configuration was inspected first.

RTR-01 showed:

```text
GigabitEthernet0/1.10  10.10.10.1  up/up
GigabitEthernet0/1.20  10.10.20.1  up/up
GigabitEthernet0/1.30  10.10.30.1  up/up
```

Its routing table also contained the correct directly connected networks.

The Windows routing table was then inspected.

STORE-PC had two active network paths:

```text
Wi-Fi:
192.168.1.x → AT&T Gateway

Ethernet:
10.10.10.21 → RTR-01
```

Both interfaces had default routes.

After Wi-Fi was disabled, inter-VLAN communication immediately worked correctly through the physical lab network.

### Root Cause

The failure was caused by the endpoint's dual network connectivity and routing behavior rather than an incorrect Cisco ROAS configuration.

### Lesson Learned

Successful network troubleshooting requires validating the entire packet path, including the endpoint's local routing table.

A failure to reach another subnet does not automatically indicate a router or switch configuration problem.

---

## 16. Phase Validation Results

| Test | Expected Result | Result |
|---|---|---|
| RTR-01 ↔ SW-01 physical link | Up | PASS |
| Gi1/0/1 trunk | 802.1Q trunking | PASS |
| Native VLAN | VLAN 99 | PASS |
| Allowed VLANs | 10,20,30,99 | PASS |
| SW-01 management SVI | 10.10.20.2 up/up | PASS |
| VLAN 10 gateway | 10.10.10.1 reachable | PASS |
| VLAN 20 gateway | 10.10.20.1 reachable | PASS |
| RTR-01 → SW-01 | ICMP successful | PASS |
| SW-01 → RTR-01 | ICMP successful | PASS |
| STORE-PC DHCP | Dynamic lease received | PASS |
| DHCP DORA | Full exchange captured | PASS |
| ARP | Request/reply observed | PASS |
| Inter-VLAN routing | VLAN 10 → VLAN 20 | PASS |
| ICMP packet capture | Request/reply observed | PASS |
| RTR-01 SSH | Remote login successful | PASS |
| SW-01 SSH | Remote login successful | PASS |
| Unused ports | VLAN 99 + shutdown | PASS |

---

## 17. Evidence

Evidence collected during Phase 1 includes:

- Cisco router and switch running configurations.
- `show interfaces trunk`
- `show vlan brief`
- `show ip interface brief`
- `show ip route`
- `show ip dhcp binding`
- `show ip dhcp pool`
- `show ip dhcp conflict`
- Windows `ipconfig /all`
- Windows routing-table verification.
- DHCP DORA Wireshark capture.
- ARP Wireshark capture.
- Inter-VLAN ICMP Wireshark capture.
- SSH connectivity verification.

Screenshots and sanitized configurations are stored in the corresponding `images/` and `configs/` directories.

---

## 18. Key Lessons Learned

This phase reinforced several networking concepts through physical implementation rather than simulation.

**Router-on-a-Stick:** A single physical router interface can provide Layer 3 gateways for multiple VLANs through 802.1Q subinterfaces.

**Trunking:** The switch and router must agree on VLAN tagging and the native VLAN for ROAS communication to function correctly.

**DHCP:** Successful address assignment can be validated both from the router's binding table and directly on the wire through the DHCP DORA exchange.

**ARP:** IPv4 communication over Ethernet requires Layer 3 addresses to be resolved to Layer 2 MAC addresses.

**Inter-VLAN routing:** Traffic between separate broadcast domains must reach a Layer 3 device before being forwarded into the destination VLAN.

**Endpoint routing matters:** A correctly configured Cisco network can still appear broken when an endpoint has competing interfaces or incorrect routes.

**Troubleshoot from the bottom up:** Physical connectivity, VLAN membership, trunking, IP addressing, routing, and application/service behavior should be validated systematically rather than assuming the first suspected component is responsible.

**Legacy compatibility:** Older network equipment may require compatibility adjustments when connecting from modern management clients.

---

## 19. Phase Conclusion

The Network Infrastructure Deployment phase established a functioning physical network baseline for the LBR-001 NOC Operations Simulation.

The completed environment now provides:

- Physical Cisco router and switch connectivity
- VLAN segmentation
- 802.1Q trunking
- Router-on-a-Stick
- Inter-VLAN routing
- Dedicated management addressing
- DHCP services
- SSH management
- ARP validation
- DHCP packet analysis
- ICMP packet analysis
- Secured unused switch ports
- Verified endpoint connectivity

The infrastructure has been tested from both the network-device and endpoint perspectives.

The network is therefore ready to serve as the baseline for the next phase of the project: **WAN Integration & Internet Services**.

---

## Project Navigation

**Current Phase:** Network Infrastructure Deployment  
**Next Phase:** WAN Integration & Internet Services  
**Future Phase:** Network Monitoring & Observability  
**Final Phase:** NOC Incident Response & Troubleshooting
