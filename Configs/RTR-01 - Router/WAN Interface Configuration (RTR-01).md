# WAN Interface Configuration (RTR-01)

WAN connectivity was configured on RTR-01 during Phase 2 to connect the internal lab network to the upstream AT&T gateway and provide a path to the Internet.

### WAN Addressing

| Setting | Value |
| --- | --- |
| WAN Interface | `Gi0/0` |
| Addressing Method | DHCP |
| WAN Address Received | `192.168.1.86/24` |
| Upstream Gateway | `192.168.1.254` |
| NAT Role | Outside |

RTR-01 receives its WAN address dynamically from the AT&T gateway through DHCP. The DHCP configuration also provides the default route used to forward Internet-bound traffic toward the upstream gateway.

---

### Configuration Commands

#### WAN Interface

```text
interface Gi0/0
 ip address dhcp
 no shutdown
```
