## VLAN Segmentation (SW-01)

VLANs are configured on `SW-01` to separate user traffic, management traffic, and unused switch ports.

### VLAN 10 — USERS

- VLAN ID: `10`
- Name: `USERS`
- Network: `10.10.10.0/24`
- Access Port: `Gi1/0/10`
- Connected Device: `STORE-PC1`
- Default Gateway: `10.10.10.1`

### VLAN 20 — MANAGEMENT

- VLAN ID: `20`
- Name: `MANAGEMENT`
- Network: `10.10.20.0/24`
- Management SVI: `Vlan20`
- SW-01 Management IP: `10.10.20.2/24`
- Default Gateway: `10.10.20.1`

### VLAN 99 — PARKING-NATIVE

- VLAN ID: `99`
- Name: `PARKING-NATIVE`
- Role: Native VLAN and unused-port isolation
- Unused switch ports are assigned to VLAN 99 and administratively disabled

### 802.1Q Trunk

- Interface: `Gi1/0/1`
- Connected Device: `RTR-01`
- Allowed VLANs: `10,20,99`
- Native VLAN: `99`
