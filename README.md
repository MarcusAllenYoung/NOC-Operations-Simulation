# 🖥️ NOC Operations Lab

> A physical networking lab focused on network deployment, visibility, troubleshooting, and incident response.

## About the Project

This project is a hands-on NOC lab built around physical Cisco networking equipment. The goal is to develop practical experience supporting a network throughout its operational lifecycle—not simply configuring devices and verifying connectivity.

The environment represents a small branch location, **LBR-001**, built using a Cisco 2911 router and Cisco Catalyst 2960-X switch. The network will progressively expand to include WAN connectivity, a virtualized monitoring server, centralized logging, and controlled network failures.

By the final stage of the project, the environment will be used to practice the workflow of a NOC technician: identifying problems, reviewing monitoring data, troubleshooting network faults, documenting incidents, escalating when necessary, restoring service, and validating recovery.

---

## 🔧 Lab Stack

| **Physical & Virtual Infrastructure** | **Network Technologies** | **NOC Operations & Monitoring** |
| :--- | :--- | :--- |
| Cisco ISR 2911 | Cisco IOS | LibreNMS |
| Cisco Catalyst 2960-X | VLANs | SNMP |
| Windows Workstation | 802.1Q Trunking | Syslog |
| Linux Monitoring Server | Router-on-a-Stick | Wireshark |
| VMware Workstation | SSH | Spiceworks |
| Network Monitoring VM | NAT/PAT | Network Alerting |

---

## 🗺️ Project Roadmap

### [01 | Network Infrastructure & Baseline 🏗️](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/Network%20Infrastructure%20Deployment%20%26%20Baseline)

Build and validate the physical LAN before introducing outside connectivity.

**Focus:** VLAN segmentation, 802.1Q trunking, inter-VLAN routing, management access, device hardening, endpoint connectivity, and establishing a known-good network baseline.

---

### [02 | WAN Integration & Internet Services 🌐](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/WAN%20Integration%20%26%20Internet%20Services)

Extend LBR-001 beyond the internal network and establish external connectivity.

**Focus:** WAN integration, default routing, NAT/PAT, Internet connectivity, DNS testing, and WAN validation.

---

### [03 | Network Monitoring & Observability 📊](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/Network%20Monitoring%20%26%20Observability%20Deployment)

Introduce centralized visibility into the health and performance of the network.

**Focus:** LibreNMS, SNMP, syslog, device availability, interface monitoring, performance baselines, and alerting.

---

### [04 | NOC Incident Response & Troubleshooting 🚨](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/NOC%20Incident%20Response%20%26%20Troubleshooting%20Operations)

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
