# Management NAT Exemption (RTR-01)

The Phase 2 NAT policy was updated during Phase 3 to support direct communication between the VLAN 20 MANAGEMENT network and NOC-SRV01.

### NAT Policy

| Setting | Value |
| --- | --- |
| Management Network | `10.10.20.0/24` |
| NOC-SRV01 | `192.168.1.50` |
| Outside Interface | Gi0/0 |
| Internet Translation | PAT |

Traffic between VLAN 20 and NOC-SRV01 is not translated, preserving the original management addresses for monitoring and administration. Normal Internet-bound traffic continues to use PAT.

---

### Configuration Commands

#### NAT Policy

```text
ip access-list extended NAT-INSIDE
 deny ip 10.10.20.0 0.0.0.255 host 192.168.1.50
 permit ip 10.10.10.0 0.0.0.255 any
 permit ip 10.10.20.0 0.0.0.255 any
```

#### PAT Overload

```text
ip nat inside source list NAT-INSIDE interface GigabitEthernet0/0 overload
```

---

⬅️ [Back to Monitoring & Alerting](../../../Monitoring%20%26%20Alerting.md)
