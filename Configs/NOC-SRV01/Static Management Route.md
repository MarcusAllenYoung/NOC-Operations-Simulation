# Static Management Route (NOC-SRV01)

A static route was configured on NOC-SRV01 to provide reachability to the branch VLAN 20 MANAGEMENT network.

### Route

| Destination | Next Hop | Purpose |
| --- | --- | --- |
| `10.10.20.0/24` | `192.168.1.86` | Branch management network |

RTR-01's WAN interface (`192.168.1.86`) acts as the next hop between NOC-SRV01 and the branch management network.

---

### Routing Path

```text
NOC-SRV01
192.168.1.50
     |
     v
RTR-01
192.168.1.86
     |
     v
VLAN 20 MANAGEMENT
10.10.20.0/24
     |
     v
SW-01
10.10.20.2
```

This route allows NOC-SRV01 to communicate directly with devices on the branch management network for monitoring and administration.

---

### Operational Route

The resulting route on NOC-SRV01 is:

```text
10.10.20.0/24 via 192.168.1.86 dev ens33
```
