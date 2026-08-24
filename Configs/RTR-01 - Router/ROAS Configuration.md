## Router-on-a-Stick (RTR-01)

Router-on-a-stick (ROAS) is configured on `RTR-01` to provide inter-VLAN routing between the USERS and MANAGEMENT networks.

### VLAN 10 — USERS

- Subinterface: `Gi0/1.10`
- VLAN ID: `10`
- Network: `10.10.10.0/24`
- Default Gateway: `10.10.10.1`
- Encapsulation: `802.1Q`

### VLAN 20 — MANAGEMENT

- Subinterface: `Gi0/1.20`
- VLAN ID: `20`
- Network: `10.10.20.0/24`
- Default Gateway: `10.10.20.1`
- Encapsulation: `802.1Q`

### VLAN 99 — NATIVE

- Subinterface: `Gi0/1.99`
- VLAN ID: `99`
- IP Address: None
- Encapsulation: `802.1Q`
- Role: Native VLAN
