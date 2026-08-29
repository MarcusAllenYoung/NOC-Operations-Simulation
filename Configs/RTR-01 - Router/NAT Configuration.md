## NAT/PAT Configuration (RTR-01)

NAT/PAT was configured on RTR-01 during Phase 2 to provide Internet access for the internal lab networks while preserving their private IPv4 addressing.

### NAT Interface Roles

| Interface | Network | NAT Role |
| --- | --- | --- |
| `Gi0/1.10` | `10.10.10.0/24` | Inside |
| `Gi0/1.20` | `10.10.20.0/24` | Inside |
| `Gi0/0` | `192.168.1.0/24` | Outside |

RTR-01 receives its WAN address through DHCP from the upstream AT&T gateway.

---

### Configuration Commands

#### NAT Inside — VLAN 10 USERS

```text
interface GigabitEthernet0/1.10
 ip nat inside
```

#### NAT Inside — VLAN 20 MANAGEMENT

```text
interface GigabitEthernet0/1.20
 ip nat inside
```

#### NAT Outside — WAN

```text
interface GigabitEthernet0/0
 ip nat outside
```

#### NAT ACL

Standard ACL 1 identifies the internal networks that are eligible for address translation.

```text
access-list 1 permit 10.10.10.0 0.0.0.255
access-list 1 permit 10.10.20.0 0.0.0.255
```

#### PAT Overload

PAT allows multiple internal devices to share the IPv4 address assigned to RTR-01 `GigabitEthernet0/0`.

```text
ip nat inside source list 1 interface GigabitEthernet0/0 overload
```
