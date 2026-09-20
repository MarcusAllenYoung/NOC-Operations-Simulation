# Incident 01 — BRANCH-PC1 Network Connectivity Incident

## Description

This incident simulates a realistic Tier 1 NOC response to an endpoint connectivity failure at **Carolina Crest Bank - Branch 01**.

The incident begins with a LibreNMS port-status alert associated with SW-01 and is worked through using a structured NOC troubleshooting process. Spiceworks is used to document the ticket lifecycle, Cisco IOS is used to investigate the affected switch interface, and an onsite contact is used when troubleshooting reaches the physical connection.

The purpose of the scenario is to demonstrate how a junior NOC technician can use monitoring data, device status, ticket documentation, remote troubleshooting, and endpoint validation to narrow the failure domain, restore service, and confirm recovery before closing the incident.

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
- **Spiceworks** - Incident ticketing
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

The incident was created in Spiceworks and assigned for investigation.

- **Organization:** Carolina Crest Bank
- **Site:** CCB-BR01
- **Severity:** 3
- **Equipment in Alarm:** SW-01
- **Priority:** Medium
- **Category:** Network

![Spiceworks Ticket Created](screenshots/INC-01/05-spiceworks-ticket-created.png)

---

## 3. Triage & Prioritization

Initial dashboard review confirmed:

- RTR-01 online
- SW-01 online
- One active port-status alert
- Gi1/0/10 down

The incident was classified as **Sev 3** because the failure appeared localized rather than site-wide.

![Initial Triage](screenshots/INC-01/06-initial-triage.png)

---

## 4. Impact Assessment

The branch router, switch, and uplink remained operational.

The available evidence showed that the incident was limited to the access connection serving **BRANCH-PC1**, rather than a complete branch outage.

---

## 5. Investigation & Diagnosis

SW-01 was accessed remotely over SSH.

Commands used:

```text
show interfaces status
show interfaces gi1/0/10
show running-config interface gi1/0/10
```

Findings:

- Gi1/0/1 remained connected
- Gi1/0/10 was `down/down (notconnect)`
- Gi1/0/10 was mapped to BRANCH_PC1
- Gi1/0/10 remained correctly configured in VLAN 10
- No interface errors were observed

![SW-01 Interface Troubleshooting](screenshots/INC-01/07-sw01-interface-troubleshooting.png)

The issue was narrowed to BRANCH-PC1 or its physical Ethernet connection.

![Investigation Update](screenshots/INC-01/08-investigation-update.png)

---

## 6. Resolution & Recovery

The onsite contact, **Alex Carter**, performed Tier 1 troubleshooting.

The BRANCH-PC1 Ethernet connection was found disconnected / not fully seated and was reseated.

![Onsite T1TS](screenshots/INC-01/09-onsite-t1ts.png)

After the corrective action, Gi1/0/10 returned to a connected state.

![Interface Recovered](screenshots/INC-01/10-interface-recovered.png)

LibreNMS also recorded the recovery and cleared the active alert.

![LibreNMS Recovery](screenshots/INC-01/11-librenms-recovery.png)

---

## 7. Validation & Closure

Service was validated directly from BRANCH-PC1.

```text
ping 10.10.10.1
ping 8.8.8.8
ping google.com
```

Validation confirmed:

- Default gateway reachable
- Internet connectivity restored
- DNS resolution working
- 0% packet loss during testing

![Gateway Validation](screenshots/INC-01/12-gateway-validation.png)

![Internet Validation](screenshots/INC-01/13-internet-validation.png)

![DNS Validation](screenshots/INC-01/14-dns-validation.png)

The final results were documented in Spiceworks and the ticket was closed.

![Ticket Closed](screenshots/INC-01/15-ticket-closed.png)

---

## 8. Post-Incident Review

**Root Cause:** Physical Ethernet connection issue affecting BRANCH-PC1 on SW-01 Gi1/0/10.

### Lessons Learned

- Monitoring identifies the symptom, not always the root cause
- Confirming what remains operational helps narrow the failure domain
- `down/down (notconnect)` pointed troubleshooting toward Layer 1
- Configuration should be verified before making changes
- Endpoint and monitoring recovery should both be validated before closure

**Final Status:** Resolved — ticket closed and site returned to proactive monitoring.
