# VLAN Configuration (SW-01)

VLANs were configured on SW-01 during Phase 1 to separate user traffic, network-management traffic, and unused switch interfaces.

### VLAN Assignment

| VLAN | Name | Purpose |
| ---: | --- | --- |
| 10 | USERS | User endpoints |
| 20 | MANAGEMENT | Network-device management |
| 99 | PARKING-NATIVE | Native VLAN and unused switch ports |

VLAN 10 provides connectivity for BRANCH-PC1, while VLAN 20 provides the dedicated management network for SW-01.

VLAN 99 is used as the native VLAN on the trunk and as the parking VLAN for unused switch interfaces.

---

### Configuration Commands

#### VLAN 10 — USERS

```text
vlan 10
 name USERS
```

#### VLAN 20 — MANAGEMENT

```text
vlan 20
 name MANAGEMENT
```

#### VLAN 99 — PARKING-NATIVE

```text
vlan 99
 name PARKING-NATIVE
```
