# 04 | Incident Response & Ticketing 🎫

This phase simulates a real-world **Tier 1 NOC incident response and ticketing environment** using the network built in the previous phases. It focuses on the full incident lifecycle from LibreNMS alert detection to Spiceworks ticket creation, initial triage, troubleshooting, onsite coordination, service restoration, recovery validation, and ticket closure. The goal is to demonstrate practical junior NOC responsibilities such as identifying incident scope, documenting troubleshooting actions, working with remote site contacts, validating service recovery, and maintaining a clear incident record from detection through resolution.

The incident workflow used throughout this phase is:

**Monitoring → Alert Detection → Ticket Creation → Initial Triage → Troubleshooting → Service Restoration → Recovery Validation → Ticket Closure**

### Incident Response Methodology

The incident workflow used in this phase was adapted from established network operations and incident-management practices documented by:

- [Cisco — Network Management System: Best Practices](https://www.cisco.com/c/en/us/support/docs/availability/high-availability/15114-NMS-bestpractice.html)
- [Microsoft Azure Well-Architected Framework — Incident Management](https://learn.microsoft.com/en-us/azure/well-architected/design-guides/incident-management)
- [Google SRE — Incident Management Guide](https://sre.google/resources/practices-and-processes/incident-management-guide/)

These references were used to guide practices such as alert detection, triage, impact assessment, fault isolation, recovery validation, documentation, and post-incident review.


- [**Incident 01**](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%2001.md) 🔌 A Sev 3 endpoint connectivity incident detected through LibreNMS and tracked in Spiceworks. Troubleshooting isolated the issue to the BRANCH-PC1 access connection, followed by onsite Tier 1 troubleshooting, physical connection restoration, connectivity validation, and ticket closure.

- [**Incident 02**](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%2002.md) 🌐 A Sev 2 branch-wide WAN outage focused on validating local network infrastructure, coordinating with onsite personnel, performing WAN troubleshooting, simulating ISP escalation, monitoring service recovery, and documenting the incident through closure.
  
