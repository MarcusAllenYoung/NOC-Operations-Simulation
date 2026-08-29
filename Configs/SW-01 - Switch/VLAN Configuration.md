## VLAN Segmentation Configuration (SW-01)

VLAN segmentation was configured on SW-01 during Phase 1 to separate user traffic, management traffic, and unused switch ports.

The design uses VLAN 10 for user devices, VLAN 20 for network management, and VLAN 99 as the native and parking VLAN.

### VLAN Design

| VLAN | Name | Purpose |
| --- | --- | --- |
| 10 | USERS | End-user devices |
| 20 | MANAGEMENT | Network device management |
| 99 | PARKING-NATIVE | Native VLAN and unused-port isolation |

---

### Configuration Commands

#### Create VLANs

```text
vlan 10
 name USERS
!
vlan 20
 name MANAGEMENT
!
vlan 99
 name PARKING-NATIVE
```

#### STORE-PC1 Access Port

STORE-PC1 connects to `GigabitEthernet1/0/10` and is assigned to VLAN 10.

```text
interface GigabitEthernet1/0/10
 description ## STORE-PC1 ##
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
```

#### Trunk to RTR-01

`GigabitEthernet1/0/1` connects SW-01 to RTR-01 and carries the routed VLANs.

```text
interface GigabitEthernet1/0/1
 description ## 802.1Q TRUNK TO RTR-01 Gi0/1 ##
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,99
```

#### Management SVI

SW-01 uses VLAN 20 for management access.

```text
interface Vlan20
 description ## MANAGEMENT SVI ##
 ip address 10.10.20.2 255.255.255.0
 no shutdown
```

The switch uses RTR-01 as its default gateway.

```text
ip default-gateway 10.10.20.1
```

---

### Unused Port Security

Unused switch ports were placed into VLAN 99 and administratively shut down.

Example:

```text
interface range GigabitEthernet1/0/2-9
 switchport mode access
 switchport access vlan 99
 shutdown
!
interface range GigabitEthernet1/0/11-24
 switchport mode access
 switchport access vlan 99
 shutdown
```

VLAN 99 prevents unused ports from being left in the default VLAN and provides a dedicated parking VLAN for inactive interfaces.
