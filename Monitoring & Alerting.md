# 03 | Monitoring & Alerting 📊

Extending the branch network with centralized monitoring, logging, time synchronization, and alerting to support NOC-style visibility and future incident-response workflows.

## 1. Purpose & Objectives 🎯

The purpose of this phase is to provide centralized visibility into the network created during Phases 1 and 2.

An Ubuntu Server virtual machine running LibreNMS was deployed as NOC-SRV01 to monitor RTR-01 and SW-01 from outside the branch network.

| Design Goal | Purpose |
|---|---|
| Remote monitoring server | Separate the monitoring system from the simulated branch network |
| Management reachability | Allow NOC-SRV01 to reach Cisco management addresses |
| NTP synchronization | Maintain consistent timestamps across monitored devices |
| SNMPv3 monitoring | Collect device and interface information securely |
| Centralized syslog | Receive Cisco event messages on NOC-SRV01 |
| LibreNMS monitoring | Provide centralized device, interface, and health visibility |
| Alerting | Detect network and monitoring failures |
| Known-good monitoring baseline | Reference point for Phase 4 incident-response simulations |

## 2. Monitoring Design 🗺️

### 💻 Technologies Used

| Technology | Purpose |
|---|---|
| VMware Workstation Pro | Hosts the NOC-SRV01 virtual machine |
| Ubuntu Server | Operating system for NOC-SRV01 |
| LibreNMS | Network monitoring, device discovery, polling, and alerting |
| SNMPv3 | Authenticated and encrypted collection of network-device information |
| Syslog / rsyslog | Centralized collection of Cisco device events |
| NTP | Time synchronization for consistent event timestamps |
| DNS | Hostname resolution required for NTP server lookup |
| SSH | Remote administration of RTR-01 and SW-01 |

### 🛠️ Equipment Used

| Equipment | Model / Platform | Role |
|---|---|---|
| Router (RTR-01) | Cisco ISR 2911 | ROAS, DHCP, NAT/PAT, SNMPv3, and syslog |
| Switch (SW-01) | Cisco Catalyst 2960S-24PS-L | VLANs, trunking, SNMPv3, and syslog |
| Monitoring Server | Ubuntu Server VM | LibreNMS monitoring and centralized syslog |
| Workstation | Windows PC | VMware host and server administration |
| Upstream Gateway | AT&T Gateway | Upstream network and Internet access |
| Cabling | Cat5e/Cat6 | Physical network connectivity |

### ⛓️ Physical Topology

### 🔀 Logical Topology

<img width="944" height="362" alt="Image" src="https://github.com/user-attachments/assets/9a447537-0233-4b77-bb2f-890f6446b7cb" />

NOC-SRV01 operates from the upstream 192.168.1.0/24 network to simulate an external monitoring location. This represents a NOC monitoring a remote branch over an upstream network rather than operating inside the branch LAN.

### 🌐 IP Addressing

| Device | Address | Purpose |
|---|---|---|
| NOC-SRV01 | 192.168.1.50/24 | Monitoring and syslog server |
| RTR-01 Gi0/0 | 192.168.1.86/24 | Branch WAN / monitoring path |
| RTR-01 Gi0/1.20 | 10.10.20.1/24 | Management VLAN gateway |
| SW-01 Vlan20 | 10.10.20.2/24 | Switch management |
| AT&T Gateway | 192.168.1.254 | Upstream gateway |

### 🛣️ Management Route

NOC-SRV01 requires a route to the branch management network through RTR-01.

`10.10.20.0/24 via 192.168.1.86`

This allows the monitoring server to reach SW-01's management interface while remaining outside the branch VLANs.

## 3. Network Configurations ⚙️

Phase 3 introduced monitoring-specific configuration on RTR-01, SW-01, and NOC-SRV01.

### 🌐 NOC-SRV01 — Monitoring Server

NOC-SRV01 was deployed as an Ubuntu Server virtual machine in VMware Workstation Pro.

The server provides:
- LibreNMS network monitoring
- SNMPv3 polling
- Centralized Cisco syslog collection
- Remote SSH access to network devices
- Management reachability to the branch network

📄 View NOC-SRV01 Phase 3 configurations below:
- [Network Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/NOC-SRV01/Network%20Configuration.md)
- [Static Management Route](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/NOC-SRV01/Static%20Management%20Route.md)
- [Syslog Receiver Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/NOC-SRV01/Syslog%20Receiver%20Configuration.md)

### 🔄 RTR-01 — Management Routing & NAT Policy

The existing Phase 2 NAT policy was modified to support the remote monitoring path.

The Phase 2 NAT configuration was updated to allow direct communication between the VLAN 20 management network and NOC-SRV01 while preserving PAT for normal Internet-bound traffic. This maintains the original management addresses between NOC-SRV01 and VLAN 20 for direct monitoring and administration.

📄 View RTR-01 Phase 3 configurations below:
- [Management NAT Exemption](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/NAT%20Phase%203%20Configuration.md)
- [NTP Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/NTP%20Configuration.md)
- [SNMPv3 Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/SNMPv3%20Configuration.md)
- [Syslog Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/RTR-01%20-%20Router/SNMPv3%20Configuration.md)

### 🔀 SW-01 — Monitoring Services

SW-01 was configured to participate in centralized monitoring and logging.

📄 View SW-01 Phase 3 configurations below:
- [NTP Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/SW-01%20-%20Switch/NTP%20Configuration.md)
- [SNMPv3 Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/SW-01%20-%20Switch/SNMPv3%20Configuration.md)
- [Syslog Configuration](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Configs/SW-01%20-%20Switch/Syslog%20Configuration.md)

## 4. Monitoring Services 📡

### 🕒 NTP Synchronization

Accurate time was configured before monitoring and logging so events from multiple systems could be correlated using consistent timestamps.

RTR-01 synchronizes with an external NTP source. SW-01 uses RTR-01 as its NTP source.

During implementation, RTR-01 initially could not resolve the hostname of the configured NTP server because DNS lookup had previously been disabled. DNS lookup was enabled on RTR-01, allowing hostname-based NTP synchronization to succeed.

### 🔐 SNMPv3 Monitoring

SNMPv3 was configured on both Cisco devices using authentication and privacy.

LibreNMS uses SNMPv3 to collect operational information including:
- Device availability
- Uptime
- Interface state
- Interface traffic
- CPU utilization
- Memory utilization
- VLAN information
- Device hardware and IOS information


### 📊 LibreNMS

LibreNMS was deployed on NOC-SRV01 and configured to monitor both Cisco devices.

| Device | Monitoring Address |
|---|---|
| RTR-01 | 192.168.1.86 |
| SW-01 | 10.10.20.2 |

LibreNMS successfully discovered device information, interfaces, operating-system information, and health metrics.

For SW-01, LibreNMS also provides visibility into interfaces such as:
- Gi1/0/1 — trunk to RTR-01
- Gi1/0/10 — STORE-PC1
- VLAN interfaces
- Unused switch interfaces

### 📝 Centralized Syslog

RTR-01 and SW-01 forward informational syslog messages to NOC-SRV01.

NOC-SRV01 receives Cisco events through rsyslog and stores them centrally for troubleshooting and incident correlation. This provides a second source of operational evidence in addition to LibreNMS monitoring.

### 🚨 Alerting

LibreNMS alerting was configured to detect meaningful network conditions.

A controlled monitoring failure was generated by temporarily removing SNMP access from SW-01. During the test:

- SW-01 remained reachable through ICMP.
- SSH management remained operational.
- SNMP polling failed.
- LibreNMS generated a critical device alert.
- SNMP access was restored.
- LibreNMS resumed successful polling and cleared the condition.

This test demonstrated that a device can remain network reachable while its monitoring service is unavailable.

**SNMP Monitoring Failure**

*[ADD SCREENSHOT: LibreNMS critical alert for SW-01]*

**Alert Recovery**

*[ADD SCREENSHOT: LibreNMS recovered/cleared alert for SW-01]*

Alert behavior was also tuned so unused or non-operational interfaces do not generate unnecessary alerts.

## 5. Verification & Operational State 🔎

### 🌐 NOC-SRV01 Network Verification

NOC-SRV01 successfully established its upstream network configuration and management route.

**Verification Commands**
```
ip addr
ip route
```

Expected management route: `10.10.20.0/24 via 192.168.1.86`

*[ADD NOC-SRV01 IP / ROUTE SCREENSHOT]*

### 📡 Management Reachability Verification

NOC-SRV01 successfully reached the branch network through RTR-01.

Connectivity was verified to:
- RTR-01 — 192.168.1.86
- SW-01 — 10.10.20.2

*[ADD MANAGEMENT REACHABILITY SCREENSHOT]*

### 🕒 RTR-01 NTP Verification

RTR-01 successfully synchronized with its external NTP source.

**Verification Commands**
```
show clock
show ntp associations
show ntp status
```

*[ADD RTR-01 NTP SCREENSHOT]*

### 🕒 SW-01 NTP Verification

SW-01 successfully synchronized its clock using RTR-01 as its NTP source.

**Verification Commands**
```
show clock
show ntp associations
show ntp status
```

*[ADD SW-01 NTP SCREENSHOT]*

### 🔐 SNMPv3 Verification

SNMPv3 polling was manually tested from NOC-SRV01 before relying on LibreNMS. Both RTR-01 and SW-01 successfully returned SNMP system information.

Sensitive authentication and privacy credentials have been removed from documentation and screenshots.

**Cisco Verification Commands**
```
show snmp user
show snmp group
```

*[ADD SANITIZED SNMPv3 VERIFICATION SCREENSHOT]*

### 📊 LibreNMS Device Verification

LibreNMS successfully monitors both network devices (RTR-01, SW-01). The dashboard provides centralized visibility into device availability and operational state.

*[ADD LIBRENMS OVERVIEW SCREENSHOT]*

### 🔀 SW-01 Monitoring Verification

LibreNMS successfully discovered SW-01 hardware, IOS information, VLANs, and physical interfaces. Operational interfaces including the router trunk and STORE-PC1 access port can be monitored from the NOC dashboard.

*[ADD SW-01 LIBRENMS DEVICE SCREENSHOT]*

### 🌐 RTR-01 Monitoring Verification

LibreNMS successfully discovered RTR-01 hardware, IOS information, interfaces, uptime, and device health information.

*[ADD RTR-01 LIBRENMS DEVICE SCREENSHOT]*

### 📝 Syslog Verification

Centralized logging was verified by generating Cisco events and confirming that they arrived on NOC-SRV01.

**Cisco Verification Command**
```
show logging
```

NOC-SRV01: Cisco messages were received in the centralized log file.

*[ADD SYSLOG VERIFICATION SCREENSHOT]*

### 🚨 Alert & Recovery Verification

A controlled SNMP monitoring failure was used to verify the alert lifecycle. LibreNMS detected the loss of SNMP polling and generated a critical alert while the switch remained reachable through other management methods. After SNMP service was restored, normal polling resumed and the monitoring condition recovered.

*[ADD ALERT SCREENSHOT]*

*[ADD RECOVERY SCREENSHOT]*

## 6. Results & Handoff 🏁

The Monitoring & Alerting phase transformed the physical branch network into a centrally monitored environment.

NOC-SRV01 now provides remote monitoring and centralized logging for RTR-01 and SW-01 using LibreNMS, SNMPv3, and syslog.

The completed monitoring environment provides visibility into:
- Device availability
- Interface state
- Device health
- Network traffic
- Time synchronization
- Centralized event logs
- Monitoring failures and recoveries

A known-good monitoring baseline has now been established. This baseline will be used during Phase 4 to generate controlled network failures, detect them through monitoring, create simulated tickets, troubleshoot the underlying problem, validate recovery, and document ticket closure.

**🎫 Next Phase: Incident Response & Ticketing**
