# Incident 01 — BRANCH-PC1 Network Connectivity Incident

## Description

This incident documents a **controlled Tier 1 NOC connectivity failure** within the NOC Operations Simulation Lab.

**Vertex Network Solutions** acts as the remote NOC responsible for monitoring and supporting **Carolina Crest Bank**. A physical connectivity failure was intentionally introduced on the branch endpoint connection to generate a real LibreNMS alert and practice the complete incident lifecycle.

This scenario is focused on **entry-level NOC / junior network operations responsibilities**, including alert review, incident triage, scope determination, basic troubleshooting, ticket documentation, onsite coordination, service restoration or escalation, recovery validation, and incident closure.

The incident was detected through LibreNMS on NOC-SRV01, documented in Spiceworks, investigated remotely through SW-01, and resolved through simulated onsite coordination with the branch contact.

---

## Objective

- Respond to a LibreNMS alert
- Determine the scope of the incident
- Perform remote troubleshooting
- Coordinate onsite Tier 1 troubleshooting
- Restore and validate service
- Document and close the incident in Spiceworks

---

## Tools Used

- **LibreNMS** - Monitoring and alerting
- **Spiceworks** - Ticketing Software
- **SW-01** - Cisco Catalyst 2960
- **BRANCH-PC1** - Windows endpoint

---

## Incident Summary

LibreNMS generated a critical port-status alert for SW-01. Initial checks confirmed that RTR-01, SW-01, and the branch uplink remained operational.

Troubleshooting isolated the issue to **Gi1/0/10**, the access port connected to BRANCH-PC1. Onsite troubleshooting identified an unsecured Ethernet connection. The cable was reseated, connectivity was restored, validation tests passed, and the Spiceworks ticket was closed.

---

## Incident Details

- **Incident ID:** INC-01
- **Title:** BRANCH-PC1 Network Connectivity Incident
- **Severity:** Sev 3
- **Status:** Resolved
- **Detection Method:** LibreNMS alert
- **Affected Device:** SW-01
- **Affected Services:** Network and Internet connectivity
- **Environment:** Lab / simulated NOC workflow

---

## Healthy Baseline

Before introducing the controlled failure, the network was verified in its normal operational state. 
This baseline provides a known-good reference before the incident was introduced.

LibreNMS confirmed:

- RTR-01 online
- SW-01 online
- No active alerts
- Gi1/0/10 operational
- BRANCH-PC1 access connection active

**RTR-01 and SW-01 Online**

<img width="1410" height="412" alt="image" src="https://github.com/user-attachments/assets/a39f4a65-e14f-43e4-ba12-f1231be47c0d" />

---
**Gi1-0-10 Online**

<img width="1344" height="364" alt="image" src="https://github.com/user-attachments/assets/46552144-a334-453f-b548-b09bbb6de31d" />

---

## 1. Detection & Alerting

The BRANCH-PC1 Ethernet connection was intentionally interrupted to create the controlled incident.

LibreNMS detected the interface state change and generated a **Critical Port Status** alert associated with SW-01

### Alert Detection

<img width="1891" height="253" alt="image" src="https://github.com/user-attachments/assets/857ad75e-14b5-4bd8-acdc-24e07da6ca68" />

---

### Unhealthy Interface State

<img width="1351" height="378" alt="image" src="https://github.com/user-attachments/assets/c310a868-bda9-4382-a1aa-094dddb5294b" />

---

## 2. Logging & Ownership

A network incident was created in Spiceworks and assigned to the NOC technician for investigation.

The ticket documented:

- Carolina Crest Bank
- Site CCB-BR01
- Severity 3
- SW-01 as equipment in alarm
- Gi1/0/10 BRANCH_PC1 down
- Medium priority
- Network category

**Spiceworks Ticket Created**

<img width="591" height="681" alt="image" src="https://github.com/user-attachments/assets/a5eb3831-1d96-4d69-a2f3-ec41f0e7f194" />

<img width="1516" height="442" alt="image" src="https://github.com/user-attachments/assets/14ffb974-20a4-41fb-a4fa-c53495556685" />

---

## 3. Triage & Prioritization

The first NOC action was to review the LibreNMS dashboard and determine whether the alert represented a single-interface problem or a larger branch outage.

Initial findings:

- RTR-01 online
- SW-01 online
- One active port-status alert
- Gi1/0/10 down

**Ticket Documentation**

<img width="1504" height="244" alt="image" src="https://github.com/user-attachments/assets/5690ade9-e9d3-4665-8dd3-b2d181aedd6d" />

---

## 4. Impact Assessment

The available evidence showed:

- RTR-01 remained reachable
- SW-01 remained reachable
- Gi1/0/1 trunk to RTR-01 remained operational
- Only the BRANCH-PC1 access connection was affected

The incident was therefore limited to a single branch endpoint and did not impact the entire Carolina Crest Bank site.

---

## 5. Investigation & Diagnosis

SW-01 was accessed remotely to investigate the affected interface.

Commands used:

```text
show interfaces status
show interfaces gi1/0/10
show running-config interface gi1/0/10
```

The investigation confirmed:

- Gi1/0/1 remained connected
- Gi1/0/10 was `down/down (notconnect)`
- Gi1/0/10 was mapped to BRANCH_PC1
- Gi1/0/10 remained configured as an access port in VLAN 10
- No interface errors were observed

**SW-01 CLI**

<img width="1344" height="421" alt="image" src="https://github.com/user-attachments/assets/8b3bde0c-81c4-4a35-9be8-783bda19a0f7" />

The failure was narrowed to the BRANCH-PC1 physical access connection.

**Ticket Documentation**

<img width="1504" height="367" alt="image" src="https://github.com/user-attachments/assets/8fd83fa1-ce91-45c2-a72b-93075c6b2635" />

---

## 6. Resolution & Recovery

The NOC contacted onsite representative **Alex Carter** for Tier 1 troubleshooting.

The onsite check confirmed:

- BRANCH-PC1 was powered on
- The Ethernet connection was checked
- The Ethernet cable was disconnected / not fully seated
- The cable was reseated

**Ticket Documentation**

<img width="1504" height="265" alt="image" src="https://github.com/user-attachments/assets/299e2a80-bcd7-4e98-92f3-14e2b023c896" />

---

**After the connection was restored, Gi1/0/10 returned to a connected state.**

<img width="1509" height="393" alt="image" src="https://github.com/user-attachments/assets/ddd93f75-b47b-4902-bd14-951a29a4e38d" />

---

**LibreNMS recorded the recovery and cleared the active port-status alert. State Goes from Red to Green.**

<img width="1879" height="301" alt="image" src="https://github.com/user-attachments/assets/5524c945-bd98-4fc3-8c81-879b125cc865" />

---

## 7. Validation & Closure

Connectivity was validated directly from BRANCH-PC1.

```text
ping 10.10.10.1 - Vlan 10 users default Gateway
ping 8.8.8.8
ping google.com
```

Validation confirmed:

- Default gateway reachable
- Internet connectivity restored
- DNS resolution working
- 0% packet loss during validation

**ping 10.10.10.1**

<img width="1918" height="427" alt="image" src="https://github.com/user-attachments/assets/365a09be-69d5-45fc-a4ba-519d10191a1e" />

---

**ping 8.8.8.8**
<img width="1918" height="370" alt="image" src="https://github.com/user-attachments/assets/26380ad3-461d-4c5d-a2d8-b695610c6978" />

---

**ping google.com**

<img width="1903" height="364" alt="image" src="https://github.com/user-attachments/assets/4cef99bb-d06e-4e44-b615-c43818aadf9e" />

---

The recovery results were documented in Spiceworks and the ticket was changed from **Open → Closed**.

<img width="1497" height="637" alt="image" src="https://github.com/user-attachments/assets/04d655cf-0a68-4ff3-9716-75d47ff9e196" />

---

## 8. Post-Incident Review

**Root Cause:** Controlled physical Ethernet connectivity failure between BRANCH-PC1 and SW-01 Gi1/0/10.

The incident demonstrated that a critical monitoring alert does not automatically mean a site-wide outage. Verifying the health of RTR-01, SW-01, and the trunk connection allowed the failure domain to be narrowed before corrective action was taken.

### Lessons Learned

- Establish what is still operational before troubleshooting deeper
- Use monitoring alerts as symptoms rather than assumptions about root cause
- `down/down (notconnect)` is strong evidence of a Layer 1 connectivity issue
- Verify configuration before making changes
- Use onsite personnel when remote troubleshooting reaches the physical layer
- Validate from the affected endpoint before closing the ticket
- Confirm LibreNMS recovery in addition to user connectivity

**Final Status:** Resolved — service restored, monitoring returned to normal, ticket closed, and Carolina Crest Bank - Branch 01 returned to proactive monitoring.
