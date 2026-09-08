# 03 | Monitoring & Alerting 📊

## Description

This phase introduces centralized monitoring and alerting to the physical network built during Phases 1 and 2.

NOC-SRV01 was added as a monitoring server running Ubuntu Server and LibreNMS. Unlike STORE-PC1, which represents a user inside the branch network, NOC-SRV01 represents a NOC monitoring system located outside of the branch LAN.

The goal was not just to install monitoring software. The network first had to provide a working management path between NOC-SRV01 and the Cisco devices. Once that path was established, NTP, SNMPv3, syslog, LibreNMS monitoring, and alerting were added and validated.

---

## Technologies Used 🧪

| Technology / Equipment | Role |
| --- | --- |
| Cisco ISR 2911 | Branch router and path into the management network |
| Cisco Catalyst 2960X | Access switch being monitored |
| VMware Workstation Pro | Hosts the monitoring server VM |
| Ubuntu Server | Operating system for NOC-SRV01 |
| LibreNMS | Centralized network monitoring and alerting |
| SNMPv3 | Secure collection of device information |
| Syslog | Centralized network event logging |
| NTP | Synchronizes device clocks |

---

## Topology

[INSERT PHASE 3 TOPOLOGY]

---

## Objectives

- Deploy NOC-SRV01 as a centralized monitoring server
- Give NOC-SRV01 a stable IP address
- Establish a route from NOC-SRV01 to the branch management network
- Preserve the original IP addresses of management traffic
- Synchronize time across the Cisco devices
- Configure secure SNMPv3 monitoring
- Add RTR-01 and SW-01 to LibreNMS
- Centralize Cisco syslog messages
- Verify monitoring and interface visibility
- Test alert detection and automatic recovery

---

## IP Addressing Scheme

| Device / Network | IP Address | Purpose |
| --- | --- | --- |
| NOC-SRV01 | 192.168.1.50/24 | Monitoring server |
| RTR-01 Gi0/0 | 192.168.1.86/24 | Router upstream / management interface |
| SW-01 Vlan20 | 10.10.20.2/24 | Switch management interface |
| VLAN 20 | 10.10.20.0/24 | Branch management network |
| AT&T Gateway | 192.168.1.254 | Upstream gateway |

---

# 1. NOC-SRV01 Deployment

NOC-SRV01 was created as an Ubuntu Server virtual machine in VMware Workstation Pro to provide a dedicated system for network monitoring.

The VM was configured to use VMware bridged networking. Bridged networking allows the VM to connect directly to the same physical network used by the Windows computer's Wi-Fi adapter instead of being hidden behind VMware's own NAT network.

Because of this, NOC-SRV01 became its own host on the `192.168.1.0/24` network.

### How NOC-SRV01 Received Its Address

NOC-SRV01 initially received network connectivity through the bridged connection to the physical Wi-Fi network.

A permanent static address was then configured:

`192.168.1.50/24`

This address belongs to the same `192.168.1.0/24` network as:

- RTR-01: `192.168.1.86`
- AT&T Gateway: `192.168.1.254`

The default gateway for NOC-SRV01 is:

`192.168.1.254`

The AT&T gateway is used as the default gateway because it provides the path from the `192.168.1.0/24` network toward other external networks and the Internet.

### Why Give NOC-SRV01 a Static Address?

A monitoring server should have a predictable address.

RTR-01 and SW-01 send syslog messages to NOC-SRV01, and other parts of the management configuration reference the monitoring server at `192.168.1.50`.

If its address changed through normal DHCP assignment, those configurations could point to the wrong address and monitoring services could stop working.

For that reason, `192.168.1.50` became the known management address for NOC-SRV01.

---

# 2. Management Network Routing

NOC-SRV01 and SW-01 are not on the same IP network.

NOC-SRV01:

`192.168.1.50/24`

SW-01:

`10.10.20.2/24`

SW-01 belongs to VLAN 20, which uses:

`10.10.20.0/24`

Because `192.168.1.0/24` and `10.10.20.0/24` are different networks, NOC-SRV01 cannot send traffic directly to SW-01 as a local host.

It needs a router.

RTR-01 is connected to both networks:

`NOC-SRV01 → RTR-01 → VLAN 20 → SW-01`

### Why Does NOC-SRV01 Need a Route to the Management Network?

NOC-SRV01 normally sends traffic for unknown networks to its default gateway:

`192.168.1.254`

That works for normal Internet traffic, but the AT&T gateway is not the router responsible for the lab's private `10.10.20.0/24` management network.

RTR-01 is.

NOC-SRV01 therefore needs to know:

> If I need to reach 10.10.20.0/24, send that traffic to RTR-01.

The route is:

`10.10.20.0/24 → 192.168.1.86`

This tells NOC-SRV01 that RTR-01 at `192.168.1.86` is the next hop for the management network.

Without this route, monitoring traffic destined for SW-01 could be sent toward the wrong gateway instead of RTR-01.

---

# 3. Stable RTR-01 Address

RTR-01's Gi0/0 interface originally received:

`192.168.1.86/24`

through DHCP from the AT&T gateway during Phase 2.

Using DHCP was enough during Phase 2 because the main goal was simply to provide outbound Internet connectivity.

Phase 3 introduced a new requirement.

NOC-SRV01 now has a route that specifically uses:

`192.168.1.86`

as its next hop.

If RTR-01 received a different DHCP address later, that route would no longer point to the router.

For that reason, the AT&T gateway was configured to reserve `192.168.1.86` for RTR-01.

RTR-01 can continue using DHCP, but the AT&T gateway will continue assigning the same address to it.

### Why Was This Not Needed in Phase 2?

The network requirements changed.

Phase 2 only required RTR-01 to receive a usable upstream address and reach the Internet.

Phase 3 introduced another device that depends on knowing exactly where RTR-01 is located.

This changed `192.168.1.86` from simply being a DHCP-learned address into part of the monitoring design.

---

# 4. NAT Exemption for Management Traffic

Phase 2 configured NAT/PAT so the private VLAN networks could access external networks.

That behavior is useful for Internet traffic.

Management traffic between NOC-SRV01 and SW-01 has a different requirement.

NOC-SRV01 needs to communicate with SW-01 using its actual management address:

`10.10.20.2`

Instead of treating this traffic like normal Internet traffic, the NAT policy was modified so traffic between VLAN 20 and NOC-SRV01 would not be translated.

### Why Exempt Management Traffic From NAT?

For normal Internet access, hiding private addresses behind RTR-01's upstream address is expected.

For this management path, preserving the original device addresses makes more sense.

LibreNMS should communicate with and identify SW-01 as:

`10.10.20.2`

rather than seeing management communication translated through:

`192.168.1.86`

The updated policy therefore performs two different jobs:

**Management traffic**

`VLAN 20 ↔ NOC-SRV01 = No NAT`

**Normal Internet traffic**

`VLAN 10 / VLAN 20 → Internet = PAT`

This allowed monitoring traffic to preserve its original addressing without breaking the Internet connectivity established during Phase 2.

---

# 5. Time Synchronization

NTP was configured before SNMP monitoring, syslog, and alert testing.

The hierarchy is:

`External NTP Source → RTR-01 → SW-01`

RTR-01 synchronizes with an external NTP source, while SW-01 uses RTR-01 as its time source.

### Why Configure NTP Before Monitoring?

Monitoring is heavily dependent on time.

If LibreNMS reports an interface failure at one time while the Cisco syslog message shows another time, determining which event happened first becomes more difficult.

Synchronized clocks allow information from different systems to be correlated during troubleshooting.

This becomes especially important during Phase 4 when an alert, ticket, troubleshooting actions, and device logs may all need to be compared.

---

# 6. SNMPv3 Configuration

SNMPv3 was configured on RTR-01 and SW-01.

LibreNMS uses SNMP to request operational information from the Cisco devices.

This includes information such as:

- Device uptime
- Interface status
- Interface traffic
- CPU utilization
- Memory utilization
- Device information
- VLAN information

### Why SNMPv3?

SNMPv3 supports authentication and privacy.

This provides a more secure monitoring method than using an SNMP community string without encryption.

Before adding the devices to LibreNMS, manual SNMPv3 queries were performed from NOC-SRV01.

### Why Test SNMP Before LibreNMS?

This separates network/SNMP problems from application problems.

If a manual SNMP query works but LibreNMS does not, the network path and SNMP configuration are already known to be working.

Troubleshooting can then focus on LibreNMS.

If the manual query fails, troubleshooting should stay focused on connectivity, routing, or SNMP before involving LibreNMS.

---

# 7. LibreNMS Device Monitoring

After connectivity and SNMPv3 were verified, RTR-01 and SW-01 were added to LibreNMS.

SW-01 is monitored through:

`10.10.20.2`

RTR-01 is monitored through:

`192.168.1.86`

### Why Use LibreNMS?

Before LibreNMS, device status had to be checked directly through individual commands on each Cisco device.

LibreNMS creates centralized visibility.

From one monitoring system, the NOC can see:

- Whether devices are reachable
- Whether interfaces are up or down
- CPU utilization
- Memory utilization
- Traffic statistics
- Device uptime
- VLAN information
- Neighbor information
- Active alerts

This changes the lab from simply having working network devices into a network that can be actively monitored.

[INSERT LIBRENMS OVERVIEW]

### SW-01

[INSERT SW-01 SCREENSHOT]

### RTR-01

[INSERT RTR-01 SCREENSHOT]

---

# 8. Centralized Syslog

RTR-01 and SW-01 were configured to send syslog messages to:

`192.168.1.50`

NOC-SRV01 collects these messages in a centralized location.

### Why Use Syslog if LibreNMS Already Monitors the Devices?

SNMP and syslog provide different information.

SNMP allows LibreNMS to poll the current state of a device.

Syslog allows the Cisco devices themselves to report events as they happen.

For example, monitoring might show that an interface is down, while a syslog message can provide additional information about when the interface changed state.

Together they provide better troubleshooting visibility than either source by itself.

---

# 9. Alert Validation

After monitoring was operational, a controlled SNMP failure was introduced on SW-01.

Normal IP connectivity remained available, but LibreNMS temporarily lost the ability to poll SW-01 through SNMP.

LibreNMS generated a critical alert.

The monitoring lifecycle was:

`Healthy → SNMP Failure → Critical Alert → SNMP Restored → Automatic Recovery`

### Why Test a Failure?

Seeing a green device in LibreNMS only proves that monitoring works while everything is healthy.

A NOC monitoring system also needs to detect when something stops working.

The controlled failure tested whether LibreNMS could:

1. Detect the problem
2. Generate an alert
3. Continue checking the device
4. Detect when the service returned
5. Automatically clear the alert

This established a known-good monitoring baseline before intentionally creating network incidents during Phase 4.

---

# Verification

## LibreNMS Overview

Both physical Cisco devices are visible and actively monitored from NOC-SRV01.

[INSERT SCREENSHOT]

## SW-01 Monitoring

LibreNMS displays SW-01's interfaces, device health, VLANs, utilization, and connection to RTR-01.

[INSERT SCREENSHOT]

## RTR-01 Monitoring

LibreNMS displays RTR-01's device health, interfaces, uptime, CPU, memory, and traffic information.

[INSERT SCREENSHOT]

## Alert Detection & Recovery

The controlled SNMP failure generated a critical alert and automatically recovered after SNMP was restored.

[INSERT SCREENSHOT]

---

# Mistakes and Troubleshooting

## Issue 1: Monitoring Server Address Changed

NOC-SRV01 originally used a different IP address while the monitoring environment was being built.

Once the final network design was established, `192.168.1.50` was configured as its permanent static address.

This provided a predictable destination for monitoring and syslog services.

## Issue 2: Management Traffic Was Affected by NAT

The Phase 2 NAT design was built for Internet connectivity.

Once Phase 3 introduced direct management communication between NOC-SRV01 and VLAN 20, the NAT policy needed to be adjusted.

Management traffic was exempted from translation while Internet-bound traffic continued using PAT.

This was not a correction to a bad Phase 2 design. It was a configuration change caused by a new requirement.

## Issue 3: NTP Hostname Resolution

DNS lookup had previously been disabled on RTR-01 to prevent mistyped IOS commands from triggering unnecessary hostname lookups.

Phase 3 introduced hostname-based NTP, which created a legitimate need for DNS resolution.

DNS lookup was enabled and RTR-01 was then able to resolve and synchronize with its external NTP source.

This demonstrated how a configuration that makes sense during one phase may need to change when new services introduce additional dependencies.

---

# Key Takeaways

- A monitoring application is only useful after the underlying network path is working.
- A static route tells NOC-SRV01 that RTR-01 is the correct next hop for the branch management network.
- Stable management addresses become important when routes, monitoring systems, and logging configurations depend on them.
- NAT is useful for Internet access but is not always desirable for direct management traffic.
- NTP allows alerts, logs, and troubleshooting activity to be correlated using consistent timestamps.
- SNMPv3 provides secure device monitoring while syslog provides event information generated by the devices.
- Testing monitoring during a failure is just as important as verifying it while the network is healthy.
- Network designs change as requirements change. Phase 3 required modifications to the Phase 2 baseline without invalidating the original Phase 2 design.
