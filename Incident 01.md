# Incident 01 - BRANCH-PC1 Network Connectivity Incident

## 1. Incident Summary

This incident documents a controlled endpoint connectivity failure at **Carolina Crest Bank — Branch 01** to practice a Tier 1 NOC incident workflow.

LibreNMS generated a critical port-status alert for SW-01. Initial triage confirmed that the router, switch, and branch uplink remained operational. Troubleshooting isolated the issue to the access connection serving **BRANCH-PC1**.

The physical Ethernet connection was restored, network connectivity was validated from BRANCH-PC1, the LibreNMS alert cleared, and the Spiceworks ticket was closed.

---

## 2. Incident Details

- **Incident ID:** INC-01
- **Title:** BRANCH-PC1 Network Connectivity Incident
- **Severity:** Sev 3
- **Status:** Resolved
- **Detection Method:** LibreNMS port-status alert
- **Alert Severity:** Critical
- **Affected Device:** SW-01
- **Affected Interface:** Gi1/0/10
- **Affected Endpoint:** BRANCH-PC1
- **Affected Services:** Endpoint network and Internet connectivity
- **Environment:** LBR-001 NOC Operations Simulation

---

## 3. Detection

Before introducing the incident, a healthy baseline was captured in LibreNMS.

RTR-01 and SW-01 were online, no active alerts were present, and Gi1/0/10 was operational and connected to BRANCH-PC1.

The Ethernet connection to BRANCH-PC1 was then intentionally disconnected to create the simulated incident.

LibreNMS detected the interface state change and generated a **Critical Port Status** alert for SW-01.

### Detection Evidence

**Healthy device baseline**

![RTR-01 and SW-01 Healthy](Screenshots/INC-01/01-devices-healthy.png)

**Gi1/0/10 healthy before the incident**

![Gi1/0/10 Healthy Baseline](Screenshots/INC-01/02-gi1-0-10-up.png)

**LibreNMS critical port-status alert**

![LibreNMS Port Alert](Screenshots/INC-01/03-librenms-port-alert.png)

**Gi1/0/10 showing down during the incident**

![Gi1/0/10 Down](Screenshots/INC-01/04-gi1-0-10-down.png)

---

## 4. Impact Assessment

Initial monitoring showed:

- RTR-01 remained online
- SW-01 remained online
- The router-to-switch uplink remained operational
- Gi1/0/10 was down
- The incident did not appear to be a complete branch outage

The available evidence indicated that the failure was localized to the access connection serving BRANCH-PC1.

The incident was classified as **Sev 3** because the impact was limited to a single endpoint rather than the entire branch.

---

## 5. Initial Triage

The incident was documented in Spiceworks and initial troubleshooting began from the Vertex Network Solutions NOC.

### Spiceworks Initial Action

```text
Current action:

Checking Dashboard
- RTR-01 online
- SW-01 online
- 1 active port status alert
- Gi1/0/10 down
- Beginning initial triage
```

SW-01 was accessed remotely over SSH and the following commands were used:

```text
show interfaces status
show interfaces gi1/0/10
show running-config interface gi1/0/10
```

The results confirmed:

- Gi1/0/1 remained connected
- Gi1/0/10 was down/down `(notconnect)`
- Gi1/0/10 was mapped to BRANCH_PC1
- Gi1/0/10 remained configured as an access port in VLAN 10
- No interface errors were observed

The issue was narrowed to BRANCH-PC1 or its physical Ethernet connection.

### CLI Findings

```text
Gi1/0/1   TRUNK_TO_RTR-01   connected
Gi1/0/10  BRANCH_PC1        notconnect
```

```text
GigabitEthernet1/0/10 is down, line protocol is down (notconnect)
Description: BRANCH_PC1
```

```text
interface GigabitEthernet1/0/10
 description BRANCH_PC1
 switchport access vlan 10
 switchport mode access
 spanning-tree portfast edge
 spanning-tree bpduguard enable
```

### Triage Evidence

**Spiceworks incident ticket**

![Spiceworks Ticket Created](Screenshots/INC-01/05-spiceworks-ticket-created.png)

**SW-01 CLI troubleshooting**

![SW-01 Initial Triage](Screenshots/INC-01/06-sw01-cli-triage.png)

**Spiceworks troubleshooting update**

![Spiceworks Triage Update](Screenshots/INC-01/07-spiceworks-triage.png)

---

## 6. Timeline of Events

| **Stage** | **Event** |
|---|---|
| T0 | RTR-01, SW-01, and Gi1/0/10 confirmed healthy |
| T1 | BRANCH-PC1 Ethernet connection intentionally disconnected |
| T2 | LibreNMS generated a critical port-status alert |
| T3 | Incident created in Spiceworks |
| T4 | Dashboard review confirmed RTR-01 and SW-01 remained online |
| T5 | SW-01 accessed remotely over SSH |
| T6 | Gi1/0/10 confirmed down/down `(notconnect)` |
| T7 | Gi1/0/10 confirmed as BRANCH_PC1 access port in VLAN 10 |
| T8 | Onsite contact engaged for Tier 1 troubleshooting |
| T9 | Ethernet connection found disconnected / not fully seated |
| T10 | Ethernet connection reseated |
| T11 | Gi1/0/10 returned to connected state |
| T12 | LibreNMS alert cleared |
| T13 | Gateway, Internet, and DNS connectivity validated |
| T14 | Spiceworks ticket closed |
| T15 | Site returned to proactive monitoring |

---

## 7. Technical Validation

After remote troubleshooting reached the physical boundary, the onsite contact **Alex Carter** was engaged for Tier 1 troubleshooting.

The BRANCH-PC1 Ethernet connection was checked and found disconnected / not fully seated.

The cable was reseated and service recovery was then validated.

### Switch Validation

SW-01 confirmed that Gi1/0/10 returned to a connected state.

```text
Gi1/0/10  BRANCH_PC1  connected  10
```

### LibreNMS Validation

LibreNMS showed the interface recovery and the active alert count returned to zero.

### BRANCH-PC1 Validation

Connectivity was tested directly from BRANCH-PC1.

```text
ping 10.10.10.1
ping 8.8.8.8
ping google.com
```

Validation confirmed:

- Default gateway reachable
- Internet connectivity restored
- DNS resolution working
- 0% packet loss during validation

### Recovery Evidence

**Gi1/0/10 restored**

![Gi1/0/10 Connected](Screenshots/INC-01/08-gi1-0-10-restored.png)

**LibreNMS recovery**

![LibreNMS Alert Cleared](Screenshots/INC-01/09-librenms-recovery.png)

**BRANCH-PC1 gateway validation**

![Gateway Ping](Screenshots/INC-01/10-branch-pc-gateway.png)

**BRANCH-PC1 Internet validation**

![Internet Ping](Screenshots/INC-01/11-branch-pc-internet.png)

**BRANCH-PC1 DNS validation**

![DNS Validation](Screenshots/INC-01/12-branch-pc-dns.png)

---

## 8. Root Cause

**Root cause:** Physical Ethernet connectivity failure between BRANCH-PC1 and SW-01 Gi1/0/10.

The Ethernet connection serving BRANCH-PC1 was disconnected / not fully seated, causing the switch interface to transition to a down/down `(notconnect)` state.

The incident was not caused by:

- RTR-01
- SW-01 device failure
- Branch uplink failure
- VLAN misconfiguration
- WAN failure

---

## 9. Resolution

The incident was resolved by reseating and securing the BRANCH-PC1 Ethernet connection.

Resolution steps:

1. Reviewed the LibreNMS alert
2. Confirmed RTR-01 and SW-01 remained online
3. Accessed SW-01 remotely over SSH
4. Identified Gi1/0/10 as down
5. Confirmed Gi1/0/10 was assigned to BRANCH_PC1 and VLAN 10
6. Contacted the onsite representative
7. Checked the physical Ethernet connection
8. Reseated the Ethernet cable
9. Confirmed Gi1/0/10 returned to connected
10. Verified gateway connectivity
11. Verified Internet connectivity
12. Verified DNS resolution
13. Confirmed LibreNMS alert recovery
14. Closed the Spiceworks ticket
15. Returned the site to proactive monitoring

### Final Spiceworks Update

```text
Current action:

Checking Dashboard
- Gi1/0/10 back online
- LibreNMS alarm cleared
- SW-01 remains online

Validating BRANCH_PC1
- Default gateway reachable
- Internet connectivity restored
- DNS resolution working
- 0% packet loss observed during validation

Resolution:
- Ethernet connection reseated
- BRANCH_PC1 connectivity restored

Next actions:
- Close TT
- Return site to proactive monitoring
```

### Closure Evidence

**Final Spiceworks resolution update**

![Spiceworks Resolution](Screenshots/INC-01/13-spiceworks-resolution.png)

**Closed Spiceworks ticket**

![Spiceworks Ticket Closed](Screenshots/INC-01/14-spiceworks-closed.png)

---

## 10. Lessons Learned

This incident reinforced several Tier 1 troubleshooting principles:

- A critical monitoring alert does not automatically mean a critical site-wide outage
- Determining what remains operational helps narrow the failure domain
- A healthy router, switch, and uplink helped eliminate upstream infrastructure as the likely cause
- `down/down (notconnect)` pointed troubleshooting toward the physical connection
- Configuration should be verified before making unnecessary changes
- Remote troubleshooting eventually reaches a physical boundary where onsite assistance may be required
- Service recovery should be validated from the affected endpoint before closing the ticket
- Monitoring should also be checked after restoration to confirm the alert condition has cleared

---

## 11. NOC Relevance

This incident reflects common entry-level NOC responsibilities:

- Monitoring network alerts
- Reviewing device and interface health
- Creating and maintaining incident tickets
- Performing initial triage
- Assessing incident scope and impact
- Using Cisco IOS for remote troubleshooting
- Narrowing the failure domain
- Coordinating with onsite personnel
- Validating network and Internet connectivity
- Confirming monitoring recovery
- Documenting resolution
- Closing the incident and returning the site to proactive monitoring

---

## 12. Related Files

- [04 | Incident Response & Ticketing](../Incident%20Response%20%26%20Ticketing.md)
- [Incident 02 - Branch WAN / ISP Outage](Incident%2002%20-%20Branch%20WAN%20ISP%20Outage.md)
