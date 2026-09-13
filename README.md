# NOC Operations Simulation 🌐

A physical networking project that simulates a fictional Bank branch network being remotely monitored and supported by a NOC technician at a Managed Service Provider (MSP).

## 🔗 About the Project

This project simulates a fictional retail store branch network, built and operated the way a NOC technician at an MSP would support a real client site.

The branch runs on physical Cisco routing and switching equipment. It begins as a standalone network and expands in phases to include Internet connectivity, remote monitoring, centralized logging, alerting, and a full ticketing workflow.

The monitoring server is intentionally placed outside the branch network on the upstream network and uses LibreNMS to monitor the router and switch. This simulates how an MSP can remotely monitor a client's network without a technician physically on-site.

Once the network is fully built and monitored, controlled failures are introduced to generate realistic incidents. Each incident is detected through a monitoring alert or simulated user report, documented as a trouble ticket, and worked from start to finish: investigated, troubleshot, restored, verified, and closed.

**The goal of this project is to practice the day-to-day work of an entry-level NOC technician at an MSP, not just build a network and consider it finished.**

---

## 🏦 Lab Environment

The lab represents a small fictional bank branch being supported remotely by a fictional MSP.

### Bank Branch (Carolina Crest Bank)

The branch contains the physical network being supported:

- Cisco ISR 2911 router
- Cisco Catalyst 2960 switch
- End-user devices

### ISP / Upstream Network

The upstream network provides external connectivity for the bank branch:

- AT&T gateway
- Upstream network connectivity
- Internet access

### MSP / NOC (Vertex Network Solutions)

The remote NOC monitors and supports the bank branch using:

- Ubuntu Linux server
- LibreNMS (monitoring software)
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

## 🗺️ Project Roadmap

### [01 | Network Deployment 🏗️](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Network%20Deployment.md) Completed ✅

Build and configure the physical bank branch network.

**Focus:** VLAN segmentation, 802.1Q trunking, inter-VLAN routing, management access, device hardening, endpoint connectivity, and establishing a known-good network baseline.

---

### [02 | Upstream Connectivity & NAT 🌐](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Upstream%20Connectivity%20%26%20NAT.md) Completed ✅

Connect the bank branch to the upstream network and provide Internet access.

**Focus:** WAN integration, NAT/PAT, Internet connectivity, DNS testing, and WAN validation.

---

### [03 | Monitoring & Alerting 📊](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md) Completed ✅

Build the remote NOC environment and begin monitoring the bank branch.

**Focus:** LibreNMS, Ubunta Linux Server, SNMP, syslog, NTP, device availability, interface monitoring, performance baselines, and alerting.

---

### [04 | Incident Response & Ticketing 🚨](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/NOC%20Incident%20Response%20%26%20Troubleshooting%20Operations)

Use the completed network and monitoring environment to simulate the work of a NOC technician supporting the bank branch.

Controlled failures are introduced into the network and handled as simulated incidents.

**Focus:** Alert investigation, ticket creation, fault isolation, troubleshooting, ISP escalation, service restoration, and incident closure.

---

## 🏁 Project Goal

The completed environment represents a fictional retail store branch being remotely supported by an MSP NOC.

The project does not end when the network is successfully configured. The network becomes a working environment where failures can be introduced, detected through monitoring, investigated, resolved, and documented through a ticketing workflow.

This allows the project to demonstrate both **building a network and supporting it after deployment.**
