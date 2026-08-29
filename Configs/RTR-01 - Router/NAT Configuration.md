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

---

### Translation Example

During validation, STORE-PC1 generated Internet traffic using its private VLAN 10 address.

- Inside Local: `10.10.10.21`
- Inside Global: `192.168.1.86`

Translation path:

`10.10.10.21 → 192.168.1.86 → Internet`

PAT uses TCP and UDP port numbers to allow multiple sessions to share the same translated address.

---

### Verification Commands

NAT/PAT operation was verified using:

```text
show ip nat translations
show ip nat statistics
show access-list 1
```

### Verification Results

Testing confirmed:

- `GigabitEthernet0/0` operating as the NAT outside interface
- `GigabitEthernet0/1.10` and `GigabitEthernet0/1.20` operating as NAT inside interfaces
- VLAN 10 traffic successfully matching ACL 1
- Active dynamic PAT translations
- STORE-PC1 translated from `10.10.10.21` to `192.168.1.86`
- Internet connectivity from STORE-PC1
- DNS traffic successfully passing through NAT
- NAT statistics recording successful translations with no misses
