# NOC Operations Simulation 🌐

A physical networking project that simulates a fictional Bank branch network being remotely monitored and supported by a NOC technician at a Managed Service Provider (MSP).

## 📖 About the Project

This project simulates a small bank branch operated by **Carolina Crest Bank** and remotely supported by **Vertex Network Solutions**, a managed service provider (MSP). Both organizations are fictional and were created for this lab simulation.

**Carolina Crest Bank - Branch 01** represents the client site. The branch contains the physical Cisco router, switch, and end-user workstation that make up the network being supported.

**Vertex Network Solutions** represents the MSP and Network Operations Center (NOC). From the NOC environment, the branch network is remotely monitored using LibreNMS, centralized syslog, and other management tools.

The **ISP / Upstream Network** provides external connectivity between the branch network and the remote monitoring environment, as well as Internet access for the branch.

The project is built in phases: first establishing the branch network, then adding upstream connectivity, remote monitoring and alerting, and finally incident response and ticketing.

Once the network is operational and monitored, controlled failures are introduced to simulate support incidents. These incidents are detected through monitoring alerts, documented in trouble tickets, investigated, troubleshot, resolved, verified, and closed.

The goal of this project is to practice the day-to-day work of an entry-level NOC technician supporting a remote client site, rather than simply configuring a network and considering it finished.

**The goal of this project is to practice the day-to-day work of an entry-level NOC technician at an MSP, not just build a network and consider it finished.**

---

## 🏦 Lab Environment

### Bank Branch (Carolina Crest Bank)

The branch contains the physical network being supported:

- Cisco ISR 2911 router
- Cisco Catalyst 2960 switch
- End-user devices

### ISP / Upstream Network

The upstream network provides external connectivity for the branch network:

- AT&T gateway
- Upstream network connectivity
- Internet access

### MSP / NOC (Vertex Network Solutions)

The remote NOC monitors and supports the branch network using:

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

Build and configure the physical network for the Carolina Crest Bank.

**Focus:** VLAN segmentation, 802.1Q trunking, inter-VLAN routing, management access, device hardening, endpoint connectivity, and establishing a known-good network baseline.

---

### [02 | Upstream Connectivity & NAT 🌐](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Upstream%20Connectivity%20%26%20NAT.md) Completed ✅

Connect the Carolina Crest Bank to the upstream network and provide Internet access.

**Focus:** WAN integration, NAT/PAT, Internet connectivity, DNS testing, and WAN validation.

---

### [03 | Monitoring & Alerting 📊](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Monitoring%20%26%20Alerting.md) Completed ✅

Build the remote NOC environment at Vertex Network Solutions, and begin monitoring the Carolina Crest Bank.

**Focus:** LibreNMS, Ubunta Linux Server, SNMP, syslog, NTP, device availability, interface monitoring, performance baselines, and alerting.

---

### [04 | Incident Response & Ticketing 🚨](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/NOC%20Incident%20Response%20%26%20Troubleshooting%20Operations)

Use the completed network and monitoring environment to simulate the work of a NOC technician supporting the branch network.

Controlled failures are introduced into the network and handled as simulated incidents.

**Focus:** Alert investigation, ticket creation, fault isolation, troubleshooting, ISP escalation, service restoration, and incident closure.

---

## 🏁 Project Goal

The project does not end when the network is successfully configured. The network becomes a working environment where failures can be introduced, detected through monitoring, investigated, resolved, and documented through a ticketing workflow.

This allows the project to demonstrate both **building a network and supporting it after deployment.**
