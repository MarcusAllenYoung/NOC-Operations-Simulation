## DHCP Configuration (RTR-01)

DHCP was configured on RTR-01 during Phase 1 to automatically assign IPv4 network settings to devices in the VLAN 10 USERS network.

### DHCP Scope

| Setting | Value |
| --- | --- |
| Network | `10.10.10.0/24` |
| Default Gateway | `10.10.10.1` |
| DNS Server | `8.8.8.8` |
| Domain Name | `noc.lab` |
| Excluded Range | `10.10.10.1 – 10.10.10.20` |

The excluded range reserves the first addresses in the subnet for infrastructure and future static assignments.

---

### Configuration Commands

#### Excluded Addresses

```text
ip dhcp excluded-address 10.10.10.1 10.10.10.20
```

#### DHCP Pool

```text
ip dhcp pool VLAN10-USERS
 network 10.10.10.0 255.255.255.0
 default-router 10.10.10.1
 dns-server 8.8.8.8
 domain-name noc.lab
```

---

### Client Assignment

STORE-PC1 successfully received its network configuration from RTR-01 through DHCP.

Assigned configuration:

- IP Address: `10.10.10.21`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `10.10.10.1`
- DNS Server: `8.8.8.8`
- DNS Suffix: `noc.lab`
<img width="1906" height="227" alt="Image" src="https://github.com/user-attachments/assets/a7c243a1-8449-4377-89f0-4867aed5be59" />

