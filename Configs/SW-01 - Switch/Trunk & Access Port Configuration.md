# Trunk & Access Port Configuration (SW-01)

Trunk and access interfaces were configured on SW-01 during Phase 1 to connect the switch to RTR-01 and provide VLAN 10 network access for STORE-PC1.

### Interface Roles

| Interface | Mode | VLAN / Role |
| --- | --- | --- |
| `Gi1/0/1` | Trunk | Connection to RTR-01 |
| `Gi1/0/10` | Access | VLAN 10 - STORE-PC1 |

The trunk between SW-01 and RTR-01 carries the VLANs required for router-on-a-stick inter-VLAN routing.

STORE-PC1 connects to `Gi1/0/10` as an access device in the VLAN 10 USERS network.

---

### Configuration Commands

#### Gi1/0/1 — Trunk to RTR-01

```text
interface GigabitEthernet1/0/1
 description TRUNK_TO_RTR-01
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
 switchport mode trunk
```

#### Gi1/0/10 — STORE-PC1

```text
interface GigabitEthernet1/0/10
 description STORE_PC
 switchport access vlan 10
 switchport mode access
```
