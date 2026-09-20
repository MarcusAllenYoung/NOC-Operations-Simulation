# Incident 01 — BRANCH-PC1 Network Connectivity Incident

## Description

This incident simulates a Tier 1 NOC response to a localized endpoint connectivity failure at **Carolina Crest Bank — Branch 01**.

LibreNMS detected the issue, Spiceworks was used to document the incident, Cisco IOS was used for troubleshooting, and BRANCH-PC1 was used to validate service recovery.

---

## Objective

- Respond to a LibreNMS alert
- Determine the scope of the incident
- Perform remote switch troubleshooting
- Coordinate onsite Tier 1 troubleshooting
- Restore and validate service
- Document and close the incident in Spiceworks

---

## Tools Used

- **LibreNMS** — Monitoring and alerting
- **Spiceworks** — Incident ticketing
- **Cisco IOS** — Remote troubleshooting
- **SW-01** — Cisco Catalyst 2960S
- **BRANCH-PC1** — Windows endpoint

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

---

## 1. Detection & Alerting

A healthy baseline was captured before the incident. RTR-01 and SW-01 were online, and Gi1/0/10 was operational.

**Healthy Devices**

<img width="1410" height="205" alt="image" src="https://github.com/user-attachments/assets/f573de28-1692-45d2-a06c-0fd9fbd41f82" />

---
**Gi1-0-10 Healthy**

<img width="1344" height="269" alt="image" src="https://github.com/user-attachments/assets/c3be5abc-9872-45a1-b6c6-8cf94287e4fe" />

---
After the BRANCH-PC1 connection was interrupted, LibreNMS generated a **Critical Port Status** alert and Gi1/0/10 transitioned down.

<img width="1891" height="253" alt="image" src="https://github.com/user-attachments/assets/857ad75e-14b5-4bd8-acdc-24e07da6ca68" />

---

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
