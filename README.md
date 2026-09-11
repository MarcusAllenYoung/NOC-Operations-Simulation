# NOC Operations Simulation 🌐

A physical networking project that simulates a fictional retail store branch network being remotely monitored and supported by a NOC technician at a Managed Service Provider (MSP).

## 🔗 About the Project

This project simulates a fictional retail store branch network, built and operated the way a NOC technician at an MSP would support a real client site.

The branch runs on physical Cisco routing and switching equipment. It begins as a standalone network and expands in phases to include Internet connectivity, remote monitoring, centralized logging, alerting, and a full ticketing workflow.

The monitoring server is intentionally placed outside the branch network on the upstream network and uses LibreNMS to monitor the router and switch. This simulates how an MSP can remotely monitor a client's network without a technician physically on-site.

Once the network is fully built and monitored, controlled failures are introduced to generate realistic incidents. Each incident is detected through a monitoring alert or simulated user report, documented as a trouble ticket, and worked from start to finish: investigated, troubleshot, restored, verified, and closed.

**The goal of this project is to practice the day-to-day work of an entry-level NOC technician at an MSP, not just build a network and consider it finished.**

---

## 🏪 Lab Environment

The lab represents a small retail store branch being supported remotely by an MSP.

### Retail Store Branch

The branch contains the physical network being supported:

- Cisco ISR 2911 router
- Cisco Catalyst 2960 switch
- End User Devices
- Internet connectivity

### MSP / NOC

The remote NOC monitors and supports the retail branch using:

- Ubuntu Linux Server
- LibreNMS (Monitoring Software)
- Remote device administration
- Incident and ticket management

---

## 🔧 Lab Technologies

<table>
  <tr>
    <td><b>1 x Cisco ISR 2911 Router</td>
    <td><b>VMware Workstation</td>
    <td><b>LibreNMS</td>
    <td><b>PuTTY</td>
  </tr>
  <tr>
    <td><b>1 x Cisco Catalyst 2960X Switch</td>
    <td><b>Ubuntu Linux</td>
    <td><b>Spiceworks</td>
    <td><b>Wireshark</td>
  </tr>
</table>

---

# NOC Operations Simulation 🌐

## 🔗 About the Project

[Your About the Project text]

---

## 🖥️ Device Reference

| **Device Name** | **Device** | **Location** |
| :--- | :--- | :--- |
| **RTR-01** | **Cisco ISR 2911 Router** | **Retail Store Branch** |
| **SW-01** | **Cisco Catalyst 2960 Switch** | **Retail Store Branch** |
| **STORE-PC1** | **Store Workstation** | **Retail Store Branch** |
| **NOC-SRV01** | **Ubuntu / LibreNMS Monitoring Server** | **MSP / NOC** |

---

## 🗺️ Network Topology

[Your topology]

---

## 🗺️ Project Roadmap

### [01 | Network Deployment 🏗️](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Network%20Deployment.md) Completed ✅

Build and validate the physical LAN before introducing outside connectivity.

**Focus:** VLAN segmentation, 802.1Q trunking, inter-VLAN routing, management access, device hardening, endpoint connectivity, and establishing a known-good network baseline.

---

### [02 | Upstream Connectivity & NAT 🌐](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Upstream%20Connectivity%20%26%20NAT.md) Completed ✅

Extend LBR-001 beyond the internal network and establish external connectivity.

**Focus:** WAN integration, default routing, NAT/PAT, Internet connectivity, DNS testing, and WAN validation.

---

### [03 | Monitoring & Alerting 📊](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md) Completed ✅

Introduce centralized visibility into the health and performance of the network.

**Focus:** LibreNMS, SNMP, syslog, device availability, interface monitoring, performance baselines, and alerting.

---

### [04 | Incident Response & Ticketing 🚨](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/NOC%20Incident%20Response%20%26%20Troubleshooting%20Operations)

Use the completed environment to simulate network incidents and operational troubleshooting.

**Focus:** Alert investigation, ticket creation, fault isolation, troubleshooting, ISP escalation, service restoration, and incident closure.

---

## 🔄 NOC Workflow

The completed environment will be used to practice a repeatable operational process:

**Monitor → Detect → Investigate → Ticket → Troubleshoot → Resolve/Escalate → Verify → Close**

Each incident will document:

- What was reported or detected
- What services or devices were affected
- Initial troubleshooting observations
- Tests performed
- Root cause
- Corrective action
- Verification of service restoration
- Final ticket resolution

---

## 🎯 Project Goal

The purpose of this project is to bridge the gap between networking knowledge and actually operating a network.

A working network is only the starting point. The completed lab will provide an environment where I can practice recognizing abnormal behavior, using monitoring data to investigate problems, troubleshooting across multiple layers of the network, communicating technical findings, and documenting incidents from detection through resolution.

The project will continue to evolve as new monitoring capabilities, troubleshooting scenarios, and infrastructure are introduced.
