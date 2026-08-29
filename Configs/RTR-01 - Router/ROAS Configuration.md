## Router-on-a-Stick Configuration (RTR-01)

Router-on-a-Stick was configured on RTR-01 during Phase 1 to provide Layer 3 gateway services for the VLANs configured on SW-01.

A single physical router interface carries multiple VLANs using 802.1Q tagging.

### VLAN Gateway Design

| VLAN | Name | Subinterface | Gateway |
| --- | --- | --- | --- |
| 10 | USERS | `Gi0/1.10` | `10.10.10.1/24` |
| 20 | MANAGEMENT | `Gi0/1.20` | `10.10.20.1/24` |
| 99 | PARKING-NATIVE | `Gi0/1.99` | No IP |

The physical interface `GigabitEthernet0/1` connects RTR-01 to SW-01 and operates as the router side of the 802.1Q trunk.

---

### Configuration Commands

#### Physical Trunk Interface

```text
interface GigabitEthernet0/1
 description ## 802.1Q TRUNK TO SW-01 Gi1/0/1 ##
 no ip address
 no shutdown
```

#### VLAN 10 — USERS

```text
interface GigabitEthernet0/1.10
 description ## VLAN 10 USERS GATEWAY ##
 encapsulation dot1Q 10
 ip address 10.10.10.1 255.255.255.0
```

#### VLAN 20 — MANAGEMENT

```text
interface GigabitEthernet0/1.20
 description ## VLAN 20 MANAGEMENT GATEWAY ##
 encapsulation dot1Q 20
 ip address 10.10.20.1 255.255.255.0
```

#### VLAN 99 — Native VLAN

```text
interface GigabitEthernet0/1.99
 description ## VLAN 99 NATIVE ##
 encapsulation dot1Q 99 native
```

VLAN 99 does not use an IP address because it is reserved as the native and parking VLAN rather than for normal endpoint traffic.

---

### Traffic Flow

Traffic from VLAN 10 reaches RTR-01 through the trunk and is processed by subinterface `Gi0/1.10`.

Traffic from VLAN 20 is processed by subinterface `Gi0/1.20`.

Example:

`STORE-PC1 → SW-01 → Gi0/1.10 → RTR-01`

RTR-01 provides the default gateway for each routed VLAN.

---

### Verification Commands

ROAS operation was verified using:

```text
show ip interface brief
show running-config interface GigabitEthernet0/1
show running-config interface GigabitEthernet0/1.10
show running-config interface GigabitEthernet0/1.20
show running-config interface GigabitEthernet0/1.99
```

Switch-side trunking was verified using:

```text
show interfaces trunk
show vlan brief
```

### Verification Results

Testing confirmed:

- `GigabitEthernet0/1` connected RTR-01 to SW-01
- VLAN 10 was mapped to `Gi0/1.10`
- VLAN 20 was mapped to `Gi0/1.20`
- VLAN 99 was configured as the native VLAN
- RTR-01 provided gateway `10.10.10.1` for VLAN 10
- RTR-01 provided gateway `10.10.20.1` for VLAN 20
- STORE-PC1 successfully reached its default gateway
- STORE-PC1 successfully reached the SW-01 management interface across the routed VLAN design
