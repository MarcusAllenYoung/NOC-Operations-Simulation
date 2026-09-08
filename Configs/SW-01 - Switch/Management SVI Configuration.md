# Management SVI Configuration (SW-01)

A Switch Virtual Interface (SVI) was configured on SW-01 during Phase 1 to provide the switch with a dedicated management address in the VLAN 20 MANAGEMENT network.

### Management Addressing

| Setting | Value |
| --- | --- |
| Management VLAN | VLAN 20 - MANAGEMENT |
| IP Address | `10.10.20.2/24` |
| Default Gateway | `10.10.20.1` |

The management SVI allows SW-01 to be remotely accessed and managed through its dedicated VLAN 20 address.

---

### Configuration Commands

#### VLAN 20 Management SVI

```text
interface Vlan20
 ip address 10.10.20.2 255.255.255.0
 no shutdown
```

#### Default Gateway

```text
ip default-gateway 10.10.20.1
```
