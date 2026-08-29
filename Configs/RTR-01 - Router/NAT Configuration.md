## NAT/PAT Configuration (RTR-01)

NAT/PAT was configured on RTR-01 during Phase 2 to provide Internet access for the internal lab networks.

### NAT Inside Interfaces

#### VLAN 10 — USERS
- Interface: `GigabitEthernet0/1.10`
- Network: `10.10.10.0/24`
- NAT Role: Inside

#### VLAN 20 — MANAGEMENT
- Interface: `GigabitEthernet0/1.20`
- Network: `10.10.20.0/24`
- NAT Role: Inside

### NAT Outside Interface

- Interface: `GigabitEthernet0/0`
- Network: `192.168.1.0/24`
- Addressing: DHCP
- NAT Role: Outside
- Upstream Gateway: `192.168.1.254`

### NAT ACL

Standard ACL 1 identifies the internal networks eligible for translation.

- `10.10.10.0/24` — USERS
- `10.10.20.0/24` — MANAGEMENT

### PAT

PAT overload uses the IP address assigned to `GigabitEthernet0/0`.

This allows multiple internal devices to access external networks while sharing RTR-01's WAN address.

### Translation Example

During validation, STORE-PC1 generated Internet traffic using:

- Inside Local: `10.10.10.21`
- Inside Global: `192.168.1.86`

Translation path:

`10.10.10.21 → 192.168.1.86 → Internet`

### Verification

NAT/PAT operation was verified using:

- `show ip nat translations`
- `show ip nat statistics`
- `show access-list 1`

Testing confirmed:

- Active dynamic translations
- VLAN 10 traffic matched ACL 1
- STORE-PC1 successfully accessed the Internet
- DNS traffic successfully passed through NAT
- NAT statistics recorded successful translations with no misses
