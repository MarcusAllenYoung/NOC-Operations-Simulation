# 03 | Monitoring & Alerting 📊

Introducing centralized network monitoring and alerting while maintaining the known-good network established during Phases 1 and 2.

---

## 1. Purpose & Objectives 🎯

The purpose of this phase is to introduce centralized monitoring and alerting to the NOC Operations Simulation. LibreNMS was deployed on an Ubuntu Server virtual machine to provide visibility into the physical Cisco network.

This phase focuses on establishing remote management connectivity, accurate time synchronization, secure SNMP monitoring, centralized syslog collection, and alert validation before moving into incident-response and ticketing simulations.

| Design Goal | Purpose |
| --- | --- |
| Remote monitoring | Allow NOC-SRV01 to monitor the branch network |
| Time synchronization | Maintain accurate timestamps across network devices |
| SNMPv3 | Securely collect device health and performance information |
| LibreNMS | Provide centralized network visibility and alerting |
| Centralized syslog | Collect Cisco system messages in one location |
| Alert validation | Verify that monitoring detects and recovers from a simulated failure |
| Known-good baseline | Reference point for future incident-response and ticketing work |

---

## 2. Network Design 🗺️

### 🛠️ Equipment Used

| Equipment | Model / Platform | Role |
| --- | --- | --- |
| Router | Cisco ISR 2911 | Routing, WAN connectivity, NAT/PAT, and management connectivity |
| Switch | Cisco Catalyst 2960X | VLANs, trunking, and monitored Layer 2 connectivity |
| NOC Server | Ubuntu Server VM | Hosts LibreNMS and centralized syslog |
| Monitoring Platform | LibreNMS | SNMP monitoring, device visibility, and alerting |
| Hypervisor | VMware Workstation Pro | Hosts NOC-SRV01 |
| Upstream Gateway | AT&T Gateway | Provides upstream network connectivity |

### ⛓️ Physical Topology

Phase 3 maintains the physical Cisco network from Phases 1 and 2 while introducing NOC-SRV01 as the centralized monitoring server.

**RTR-01 & SW-01 (Lab Equipment)**

[INSERT PHYSICAL LAB IMAGE]

**NOC-SRV01 (VMware Workstation)**

[INSERT NOC-SRV01 / LIBRENMS IMAGE]

### 🔀 Logical Topology

[INSERT PHASE 3 LOGICAL TOPOLOGY IMAGE]

### 🔌 Interface Mapping

| Device | Interface | Role |
| --- | --- | --- |
| RTR-01 | Gi0/0 | Upstream / management connectivity |
| RTR-01 | Gi0/1 | Trunk to SW-01 |
| RTR-01 | Gi0/1.10 | VLAN 10 gateway |
| RTR-01 | Gi0/1.20 | VLAN 20 management gateway |
| RTR-01 | Gi0/1.99 | Native VLAN |
| SW-01 | Gi1/0/1 | Trunk to RTR-01 |
| SW-01 | Vlan20 | Switch management interface |
| SW-01 | Gi1/0/10 | STORE-PC1 |
| NOC-SRV01 | ens33 | Monitoring server network interface |

### 🌐 Monitoring & IP Addressing

| Network / Device | Address | Purpose |
| --- | --- | --- |
| VLAN 10 | 10.10.10.0/24 | USERS network |
| VLAN 20 | 10.10.20.0/24 | MANAGEMENT network |
| SW-01 | 10.10.20.2/24 | Switch management |
| RTR-01 Gi0/0 | 192.168.1.86/24 | Upstream / management address |
| NOC-SRV01 | 192.168.1.50/24 | LibreNMS monitoring server |
| AT&T Gateway | 192.168.1.254 | Upstream gateway |

NOC-SRV01 uses a static address of `192.168.1.50/24` and a route to the management network `10.10.20.0/24` through RTR-01 at `192.168.1.86`.

During Phase 3, the existing NAT policy from Phase 2 was modified to exempt management traffic between VLAN 20 and NOC-SRV01 from translation while preserving PAT for normal Internet-bound traffic.

---

## 3. Network Implementation ⚙️

Phase 3 introduced centralized monitoring while preserving the VLANs, routing, DHCP, WAN connectivity, and Internet access established during the previous phases.

### 🖥️ NOC-SRV01 (Monitoring Server)

An Ubuntu Server virtual machine named NOC-SRV01 was deployed in VMware Workstation Pro to host LibreNMS.

Linux and LibreNMS were new technologies for this project, so documentation and guidance were used to deploy the server and monitoring platform. The primary focus of this phase was the networking side of the deployment: establishing connectivity to the Cisco devices, configuring secure monitoring, and validating network visibility.

- Static address: `192.168.1.50/24`
- Default gateway: `192.168.1.254`
- Route to `10.10.20.0/24` through `192.168.1.86`
- LibreNMS used as the centralized monitoring platform
- Centralized syslog collection enabled

### 🕐 Time Synchronization

NTP was configured before monitoring services so that network events and logs would have accurate and consistent timestamps.

RTR-01 synchronizes with an external NTP source, and SW-01 uses RTR-01 as its NTP source.

**NTP Hierarchy**

`External NTP Source → RTR-01 → SW-01`

This provides consistent timestamps for monitoring, syslog messages, and future incident-response documentation.

### 🔐 SNMPv3 Monitoring

SNMPv3 was configured on RTR-01 and SW-01 to provide authenticated and encrypted monitoring.

📄 [View SW-01 Phase 3 Configurations](INSERT-SW-01-PHASE-3-CONFIG-LINK)

📄 [View RTR-01 Phase 3 Configurations](INSERT-RTR-01-PHASE-3-CONFIG-LINK)

- SNMPv3 authentication and privacy enabled
- RTR-01 added to LibreNMS
- SW-01 added to LibreNMS
- Device health and interface information collected
- SNMP credentials removed from public documentation and screenshots

### 📊 LibreNMS Monitoring

LibreNMS was configured to monitor both physical Cisco devices.

The monitoring platform provides centralized visibility into:

- Device availability
- Interface status
- Traffic utilization
- CPU utilization
- Memory utilization
- Device uptime
- VLAN information
- Neighbor information
- Device alerts

### 📝 Centralized Syslog

RTR-01 and SW-01 were configured to forward system messages to NOC-SRV01.

This provides a centralized location for reviewing network events and gives additional information that can be used during troubleshooting and incident-response simulations.

---

## 4. Validation & Troubleshooting 🔎

### 📡 Remote Management Connectivity Verification

Connectivity between NOC-SRV01 and the branch management network was verified.

NOC-SRV01 successfully reached SW-01 at `10.10.20.2` through RTR-01.

The management path is:

`NOC-SRV01 → RTR-01 → VLAN 20 → SW-01`

The NAT exemption allows management traffic between VLAN 20 and NOC-SRV01 to retain its original addressing while normal Internet-bound traffic continues to use PAT.

[INSERT CONNECTIVITY / ROUTING VERIFICATION IMAGE]

---

### 🕐 NTP Verification

Time synchronization was verified on both Cisco devices.

RTR-01 successfully synchronized with an external NTP source, and SW-01 successfully synchronized with RTR-01.

This confirmed that the devices had a consistent time reference before monitoring and centralized logging were fully validated.

[INSERT NTP VERIFICATION IMAGE]

### 🔐 SNMPv3 Verification

SNMPv3 communication was tested from NOC-SRV01 before relying on LibreNMS polling.

Successful SNMPv3 queries confirmed that NOC-SRV01 could securely retrieve device information from both RTR-01 and SW-01.

[INSERT SNMPv3 VERIFICATION IMAGE]

### 📊 LibreNMS Device Verification

Both Cisco devices were successfully discovered and monitored by LibreNMS.

**LibreNMS Overview**

[INSERT LIBRENMS OVERVIEW IMAGE]

**SW-01 Monitoring**

LibreNMS successfully identified SW-01 and displayed device information, interface status, uptime, CPU and memory utilization, VLAN information, and the trunk connection to RTR-01.

[INSERT SW-01 LIBRENMS IMAGE]

**RTR-01 Monitoring**

LibreNMS successfully identified RTR-01 and displayed device information, interface status, uptime, CPU and memory utilization, and network connectivity information.

[INSERT RTR-01 LIBRENMS IMAGE]

### 📝 Syslog Verification

Centralized syslog was validated by confirming that messages from both RTR-01 and SW-01 were received by NOC-SRV01.

This provided a second source of operational information in addition to SNMP monitoring.

[INSERT SYSLOG VERIFICATION IMAGE]

### 🚨 Alert Validation

A controlled monitoring failure was introduced on SW-01 to verify the LibreNMS alerting process.

SNMP monitoring was temporarily interrupted while normal IP connectivity remained available. LibreNMS detected that SW-01 was no longer reachable through SNMP and generated a critical device alert.

**Alert Lifecycle**

`Healthy → SNMP Failure → Critical Alert → Service Restored → Automatic Recovery`

After SNMP monitoring was restored, LibreNMS automatically detected the recovery and returned the device to a healthy state.

[INSERT LIBRENMS ALERT IMAGE]

This test confirmed that LibreNMS could detect a monitoring failure, generate an alert, and recognize when the affected service recovered.

---

## 5. Results & Handoff 🏁

The Monitoring & Alerting phase successfully introduced centralized visibility into the physical Cisco network established during Phases 1 and 2.

NOC-SRV01 now provides LibreNMS monitoring and centralized syslog collection for RTR-01 and SW-01. SNMPv3 provides secure device monitoring, NTP provides consistent timestamps, and the management routing and NAT exemption allow the monitoring server to communicate with the branch management network.

The controlled SNMP failure also demonstrated the complete monitoring lifecycle from normal operation through detection, alerting, restoration, and automatic recovery.

This known-good monitoring baseline will be used during the next phase to create realistic network incidents, respond to alerts, troubleshoot the underlying issue, and document the entire process through a simulated ticketing workflow.

🎫 Next Phase: [Incident Response & Ticketing](INSERT-PHASE-4-LINK)
