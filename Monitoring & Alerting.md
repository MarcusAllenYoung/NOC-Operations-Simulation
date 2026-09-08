# 03 | Monitoring & Alerting 📊

## Description

[Short explanation of what Phase 3 adds to the NOC lab and why.]

## Technologies Used 🧪

[Equipment / software table]

## Topology

[Phase 3 topology image]

[Device / role table]

## Objective

- Deploy NOC-SRV01
- Establish management connectivity
- Configure NTP
- Configure SNMPv3
- Deploy LibreNMS monitoring
- Configure centralized syslog
- Configure alerting
- Validate failure detection and recovery

## IP Addressing Scheme

[Network/subnet table]

## Logical Addressing Table

[Device/interface/IP/gateway table]


# 1. Configuration Steps

## NOC-SRV01 Network Configuration

1. Deploy Ubuntu Server in VMware Workstation.
2. Configure VMware networking in Bridged mode.
3. Bridge the VM to the physical Wi-Fi adapter.
4. Configure NOC-SRV01 with:
   - IP Address: `192.168.1.50`
   - Subnet Mask: `/24`
   - Default Gateway: `192.168.1.254`
   - DNS: `192.168.1.254`, `8.8.8.8`
5. Verify local and Internet connectivity.

### Why?

NOC-SRV01 is being used as the centralized monitoring server. Bridged networking places the VM directly on the `192.168.1.0/24` network instead of behind VMware NAT.

The static `192.168.1.50` address gives the monitoring server a predictable address because network devices will send management services such as syslog to it.


# 2. Management Route Configuration

NOC-SRV01 is on `192.168.1.0/24`, while SW-01's management interface is on the separate `10.10.20.0/24` network.

A route was configured on NOC-SRV01:

`10.10.20.0/24 → 192.168.1.86`

### Why?

NOC-SRV01's default gateway is the AT&T gateway at `192.168.1.254`, but the AT&T gateway is not responsible for routing the lab's VLAN 20 network.

RTR-01 knows how to reach VLAN 20.

The route tells NOC-SRV01:

`To reach 10.10.20.0/24 → send the traffic to RTR-01 at 192.168.1.86`

The resulting management path is:

`NOC-SRV01 → RTR-01 → VLAN 20 → SW-01`

[SCREENSHOT]


# 3. RTR-01 Address Reservation

RTR-01 originally received `192.168.1.86` dynamically from the AT&T gateway during Phase 2.

The AT&T gateway was configured to continue assigning `192.168.1.86` to RTR-01.

### Why?

During Phase 2, RTR-01 only needed a valid upstream address for Internet connectivity.

Phase 3 introduced a new dependency because NOC-SRV01 now uses `192.168.1.86` as the next hop for reaching VLAN 20.

If RTR-01's DHCP address changed, the route on NOC-SRV01 would point to the wrong address.

This allows RTR-01 to remain a DHCP client while maintaining a predictable upstream address.


# 4. NAT Exemption Configuration

The Phase 2 NAT policy was modified to exempt management traffic between VLAN 20 and NOC-SRV01 from translation.

Management traffic:

`10.10.20.0/24 ↔ 192.168.1.50 = No NAT`

Normal Internet traffic:

`VLAN 10 / VLAN 20 → Internet = PAT`

### Why?

PAT is useful for Internet-bound traffic, but the monitoring server needs direct communication with the real management addresses of the network devices.

LibreNMS monitors SW-01 using:

`10.10.20.2`

The NAT exemption allows this management communication to retain its original addressing while preserving the Internet connectivity built during Phase 2.

[SCREENSHOT]


# 5. NTP Configuration

RTR-01 was configured to synchronize with an external NTP source.

SW-01 was then configured to use RTR-01 as its NTP source.

`External NTP Source → RTR-01 → SW-01`

### Why?

Monitoring, syslog, alerts, and troubleshooting all depend on timestamps.

Synchronizing the network devices allows events from different systems to be compared accurately during troubleshooting.

[SCREENSHOT]


# 6. SNMPv3 Configuration

SNMPv3 was configured on both Cisco devices.

## SW-01

1. Create the SNMPv3 monitoring group.
2. Create the SNMPv3 monitoring user.
3. Enable authentication.
4. Enable privacy/encryption.
5. Test SNMPv3 communication from NOC-SRV01.

## RTR-01

1. Create the SNMPv3 monitoring group.
2. Create the SNMPv3 monitoring user.
3. Enable authentication.
4. Enable privacy/encryption.
5. Test SNMPv3 communication from NOC-SRV01.

> Authentication and privacy credentials are intentionally excluded from this repository.

### Why?

LibreNMS uses SNMP to collect operational information such as interface status, traffic, CPU utilization, memory utilization, uptime, and device information.

SNMPv3 was used because it provides authentication and encryption.

Manual SNMP testing was performed before adding the devices to LibreNMS. This verified that the network path and SNMP configuration worked independently of the monitoring application.

[SCREENSHOT]


# 7. LibreNMS Device Configuration

After SNMPv3 communication was verified, both physical Cisco devices were added to LibreNMS.

## SW-01

1. Add `10.10.20.2` to LibreNMS.
2. Select SNMPv3.
3. Configure SNMP authentication.
4. Start device discovery and polling.
5. Verify device and interface information.

## RTR-01

1. Add `192.168.1.86` to LibreNMS.
2. Select SNMPv3.
3. Configure SNMP authentication.
4. Start device discovery and polling.
5. Verify device and interface information.

### Why?

LibreNMS provides a centralized view of the network instead of requiring each Cisco device to be checked individually.

The NOC can now view device availability, interfaces, utilization, uptime, VLAN information, neighbors, and alerts from one monitoring platform.

[LIBRENMS OVERVIEW SCREENSHOT]


# 8. Centralized Syslog Configuration

RTR-01 and SW-01 were configured to forward informational syslog messages to:

`192.168.1.50`

Messages from both Cisco devices were verified on NOC-SRV01.

### Why?

SNMP and syslog provide different types of operational visibility.

SNMP allows LibreNMS to poll the current state of the devices.

Syslog allows the devices to report events when they occur.

Together, they provide additional information that can be correlated during troubleshooting.

[SCREENSHOT]


# 9. LibreNMS Alert Configuration

LibreNMS alerting was reviewed after both devices were being successfully monitored.

Interfaces that were intentionally unused were excluded from port-status alerts, while operationally important interfaces remained monitored.

Important monitored interfaces include:

- `Gi1/0/1` — trunk to RTR-01
- `Gi1/0/10` — STORE-PC1

### Why?

Not every down interface represents a problem.

Ignoring interfaces that are intentionally unused reduces unnecessary alerts while keeping visibility on interfaces that could represent a real network incident.


# Verification

## LibreNMS Device Monitoring

Both physical Cisco devices were successfully discovered and monitored.

[LIBRENMS OVERVIEW]

## SW-01 Interface Monitoring

LibreNMS successfully displayed SW-01's interfaces, including the trunk and STORE-PC1 access port.

[SW-01 SCREENSHOT]

## RTR-01 Monitoring

LibreNMS successfully displayed RTR-01's device health and interface information.

[RTR-01 SCREENSHOT]

## Alert Detection and Recovery

A controlled SNMP failure was introduced on SW-01.

LibreNMS detected the failure and generated a critical alert.

After SNMP was restored, LibreNMS automatically detected the recovery.

`Healthy → SNMP Failure → Critical Alert → SNMP Restored → Recovery`

[ALERT SCREENSHOT]


# Mistakes and Troubleshooting

## Issue 1: NTP Hostname Resolution

DNS lookup had previously been disabled on RTR-01.

When hostname-based NTP was introduced during Phase 3, RTR-01 needed DNS resolution to locate the external NTP source.

DNS lookup was enabled, allowing RTR-01 to resolve the NTP hostname and synchronize successfully.

This demonstrated that a configuration that made sense earlier in the project may need to change when a new service introduces another dependency.


## Issue 2: Management Traffic and NAT

The Phase 2 NAT configuration was designed for Internet connectivity.

When Phase 3 introduced direct management communication between NOC-SRV01 and VLAN 20, the NAT policy needed to be modified.

Management traffic was exempted from translation while normal Internet-bound traffic continued using PAT.

This was not a correction to a bad Phase 2 design. It was a configuration change caused by a new network requirement.


# Key Takeaways

- NOC-SRV01 needs a stable address because other network devices and services depend on knowing where the monitoring server is located.
- A static route directs NOC-SRV01 toward RTR-01 instead of the AT&T gateway when reaching the VLAN 20 management network.
- Stable next-hop addressing became necessary once NOC-SRV01 depended on RTR-01 for management routing.
- NAT requirements can change when a network adds direct management communication.
- NTP provides consistent timestamps for monitoring, logging, and troubleshooting.
- SNMPv3 provides authenticated and encrypted device monitoring.
- Syslog and SNMP provide different but complementary sources of operational visibility.
- A monitoring system should be tested during both healthy and failed conditions.
  
