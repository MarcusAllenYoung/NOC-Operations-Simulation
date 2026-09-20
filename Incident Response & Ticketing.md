# 04 | Incident Response & Ticketing 🎫

## Overview

This phase demonstrates a realistic **NOC-style incident response and ticketing workflow** using the network setup in the previous phases.

The goal of this phase is to demonstrate how monitoring alerts are reviewed, investigated, documented, and worked through to verified service recovery.

This phase focuses on **entry-level NOC / junior network operations** responsibilities such as:

- monitoring network alerts
- determining incident scope
- performing initial troubleshooting
- reviewing Cisco interface status
- documenting actions in Spiceworks
- coordinating with onsite personnel
- validating service recovery
- closing incident tickets

---

## Objective

The objective of this phase is to practice a complete NOC incident workflow:

**monitoring**
→ **alert detection**
→ **ticket creation**
→ **initial triage**
→ **troubleshooting**
→ **service restoration**
→ **recovery validation**
→ **ticket closure**

The focus is on operational troubleshooting and documentation rather than adding additional network infrastructure.

---

## Incidents

### Incident 01 — BRANCH-PC1 Network Connectivity Incident

**Severity:** Sev 3  
**Status:** Resolved

LibreNMS detected a port-status alert on SW-01. Troubleshooting isolated the issue to the BRANCH-PC1 access connection. The physical connection was restored, connectivity was validated, and the Spiceworks ticket was closed.

[View Incident 01](Incident%2001%20-%20BRANCH-PC1%20Connectivity%20Incident.md)

---

### Incident 02 — Branch WAN / ISP Outage

**Severity:** Sev 2  
**Status:** Planned

A simulated branch-wide WAN outage involving local troubleshooting, onsite coordination, simulated ISP escalation, recovery validation, and ticket closure.

[View Incident 02](Incident%2002%20-%20Branch%20WAN%20ISP%20Outage.md)

With the first incident successfully completed, the next scenario will focus on a branch-wide WAN outage and simulated ISP escalation.
