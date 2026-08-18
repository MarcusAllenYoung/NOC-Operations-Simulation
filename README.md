# NOC Operations Lab 🖥️

This project documents the development of a physical network lab designed around the responsibilities of an entry-level Network Operations Center (NOC) technician. The environment begins with physical Cisco routing and switching infrastructure and gradually expands to include WAN connectivity, network monitoring, centralized logging, and simulated incident response.

The goal is to build practical experience supporting a network after deployment—not just configuring it. The completed environment will be used to establish network baselines, monitor infrastructure health, investigate alerts, troubleshoot connectivity problems, document incidents, and verify service restoration.

## Lab Environment

| **Cisco 2911 Router** | **Cisco Catalyst 2960-X** |
| --------------------- | ------------------------- |
| Cisco IOS             | Windows PC                |
| Linux Server          | Virtualization            |
| LibreNMS              | Wireshark                  |
| SNMP                  | Syslog                     |

The lab represents a small branch network identified as **LBR-001**. The network is being developed in four phases, with each phase building on the working environment created during the previous stage.

The first phase establishes the physical LAN and a known-good baseline. WAN connectivity is then introduced, followed by centralized monitoring and logging. The final environment will be intentionally subjected to network failures so that incidents can be detected, investigated, resolved or escalated, and documented using a structured NOC workflow.

### Project Workflow

**Build → Connect → Monitor → Troubleshoot → Restore → Document**

## Lab Phases

- [NOC Incident Response & Troubleshooting Operations](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/Network%20Infrastructure%20Deployment%20%26%20Baseline) — Deployment of the physical Cisco infrastructure, including VLAN segmentation, 802.1Q trunking, router-on-a-stick, secure management, and establishment of a known-good network baseline.

- [WAN Integration & Internet Services](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/WAN%20Integration%20%26%20Internet%20Services) — Integration of the branch network with an upstream network, including routing, NAT/PAT, Internet connectivity, and WAN validation.

- [Network Monitoring & Observability Deployment](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/Network%20Monitoring%20%26%20Observability%20Deployment) — Deployment of centralized network monitoring and logging using Linux, LibreNMS, SNMP, syslog, alerting, and performance monitoring.

- [NOC Incident Response & Troubleshooting Operations](https://github.com/MarcusAllenYoung/NOC-Operations-Lab/blob/main/NOC%20Incident%20Response%20%26%20Troubleshooting%20Operations) — Simulation of NOC incidents involving monitoring alerts, ticket documentation, fault isolation, troubleshooting, escalation, service restoration, and verification.

## Project Objective

The objective of this lab is to demonstrate hands-on experience with the operational side of networking. By the end of the project, the same environment that was initially built and validated will be used to practice monitoring, troubleshooting, incident response, and technical documentation similar to the workflows encountered in a NOC environment.
