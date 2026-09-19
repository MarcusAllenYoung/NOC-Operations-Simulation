Coming Soon ⌛
# Ticket 01 — BRANCH-PC1 Network Connectivity Incident

> **LBR-001 NOC Operations Simulation**  
> Alert-driven incident involving an isolated endpoint connectivity failure at Carolina Crest Bank — Branch 01.

---

## Incident Overview

| Field | Details |
|---|---|
| Organization | Carolina Crest Bank |
| Site | Carolina Crest Bank — Branch 01 |
| Site ID | CCB-BR01 |
| NOC | Vertex Network Solutions |
| Ticket | TT-00000001 |
| Severity | 3 |
| Priority | Medium |
| Category | Network |
| Equipment in Alarm | SW-01 |
| Affected Endpoint | BRANCH-PC1 |
| Affected Interface | Gi1/0/10 |
| Monitoring | LibreNMS |
| Ticketing | Spiceworks |
| Technician | Marcus Young |
| Onsite Contact | Alex Carter |
| Final Status | Closed |

---

## Objective

The objective of this incident was to practice a complete NOC troubleshooting workflow from monitoring alert through ticket closure.

The scenario demonstrates:

- Alert monitoring with LibreNMS
- Incident creation in Spiceworks
- Determining incident scope
- Cisco switch interface troubleshooting
- Isolating an endpoint connectivity failure
- Coordinating Tier 1 troubleshooting with an onsite contact
- Restoring network service
- Validating endpoint and monitoring recovery
- Documenting and closing the incident

---

## Incident Scenario

A controlled network connectivity failure was introduced between **BRANCH-PC1** and **SW-01**.

LibreNMS detected a network condition associated with SW-01.

At the start of the incident, the root cause was intentionally unknown from the NOC perspective.

The troubleshooting objective was to determine whether the problem involved:

- The entire branch
- RTR-01
- SW-01
- The branch uplink
- An individual access interface
- The endpoint or its physical connection

---

# 1. Healthy Baseline

Before introducing the failure, the network was verified to be operating normally.

### Baseline Status

```text
RTR-01                 UP
SW-01                  UP
SW-01 Gi1/0/1          UP
SW-01 Gi1/0/10         UP
BRANCH-PC1             CONNECTED
Internet Connectivity  WORKING
LibreNMS               HEALTHY
```

Connectivity was also verified from BRANCH-PC1.

```text
ping 10.10.10.1
ping 10.10.20.2
ping 8.8.8.8
ping google.com
```

### Evidence — Healthy Baseline

![Healthy LibreNMS Baseline](screenshots/ticket01-01-healthy-baseline.png)

**What this proves:** RTR-01 and SW-01 were healthy before the incident occurred.

---

# 2. Incident Detection

After the controlled failure was introduced, LibreNMS detected a network condition associated with SW-01.

At this point, the affected endpoint and root cause had **not** been identified.

### Initial Alert

```text
Equipment in Alarm:
SW-01

Current Action:
- Reviewing LibreNMS alerts
- Checking device and interface status
- Beginning troubleshooting to determine incident scope
```

### Evidence — LibreNMS Alert

![LibreNMS Alert](screenshots/ticket01-02-librenms-alert.png)

**What this proves:** The incident was detected through monitoring rather than starting with knowledge of the root cause.

---

# 3. Spiceworks Ticket Creation

A Spiceworks incident was created using only the information available at detection time.

### Ticket Information

```text
TT: 00000001
Carolina Crest Bank
Site #: CCB-BR01
Severity 3
Priority: Medium
Category: Network

Summary:
Carolina Crest Bank - Branch Network Connectivity Alert

Equipment in Alarm:
SW-01
```

### Initial Description

```text
LibreNMS detected a network connectivity issue at
Carolina Crest Bank — Branch 01.

SW-01 is currently associated with the alert.

Current Action:
- Reviewing LibreNMS alerts
- Checking device and interface status
- Beginning troubleshooting to determine the scope of the issue
```

### Evidence — Initial Ticket

![Spiceworks Ticket](screenshots/ticket01-03-ticket-created.png)

**What this proves:** The incident was formally documented before the cause was known.

---

# 4. Initial NOC Triage

The first troubleshooting goal was to determine whether this was a **site-wide outage or an isolated connectivity problem**.

### Dashboard Review

```text
Checking Dashboard
- RTR-01 online
- SW-01 online
- SW-01 Gi1/0/1 uplink operational
- Branch infrastructure remains reachable
```

### Assessment

RTR-01, SW-01, and the switch uplink were still operational.

This ruled against:

```text
Complete branch outage
Router outage
Switch outage
Branch uplink outage
```

The problem appeared to be farther downstream.

### Ticket Update

```text
Initial Triage:

- RTR-01 remains online
- SW-01 remains online
- Gi1/0/1 uplink remains operational
- Branch infrastructure remains reachable

Assessment:
Incident appears isolated rather than site-wide.

Next Action:
Review SW-01 access interfaces.
```

### Evidence — Initial Triage

![Initial Triage](screenshots/ticket01-04-initial-triage.png)

**What this proves:** The upstream branch infrastructure remained healthy during the incident.

---

# 5. Remote Investigation

SW-01 was accessed remotely to inspect interface status.

### Commands Used

```text
show interfaces status
show interfaces gi1/0/10
```

### Findings

```text
SW-01                ONLINE
Gi1/0/1              CONNECTED
Gi1/0/10             NOT CONNECTED
```

The failure had now been narrowed to a specific access interface.

### Interface Mapping

Network documentation identified:

```text
SW-01 Gi1/0/10
       |
       |
 BRANCH-PC1
```

### Assessment

The incident was isolated to either:

```text
BRANCH-PC1
or
its physical Ethernet connection
```

### Ticket Update

```text
Remote Troubleshooting:

- Connected remotely to SW-01
- Reviewed interface status
- Gi1/0/1 remains operational
- Gi1/0/10 is not connected
- Gi1/0/10 is assigned to BRANCH-PC1

Assessment:
Issue appears isolated to BRANCH-PC1 or its physical Ethernet connection.

Next Action:
Contact onsite representative for Tier 1 Troubleshooting.
```

### Evidence — Cisco Interface Status

![SW-01 Interface Status](screenshots/ticket01-05-interface-investigation.png)

**What this proves:** Gi1/0/10 was down while the switch and uplink remained operational.

---

# 6. Site Contact and Tier 1 Troubleshooting

Marcus contacted **Alex Carter**, the onsite representative at Carolina Crest Bank — Branch 01.

### Site Contact

```text
Calling site
- Alex Carter answered
- Informed Alex that BRANCH-PC1 appears to have lost connectivity
- Requested Tier 1 Troubleshooting (T1TS)
```

Alex was asked to inspect the physical Ethernet connection.

### T1TS

```text
T1TS with Alex
- Located BRANCH-PC1
- Inspected Ethernet connection
- Ethernet cable was not fully seated
- Requested cable be reseated and secured
```

### Root Cause Identified

```text
Physical Ethernet connection to BRANCH-PC1 was not fully seated.
```

### Evidence — Spiceworks Troubleshooting Update

![Site Contact Update](screenshots/ticket01-06-site-contact.png)

**What this proves:** The onsite physical check confirmed the condition suspected from the switch interface status.

---

# 7. Service Restoration

Alex reseated the Ethernet cable connected to BRANCH-PC1.

SW-01 was checked again.

### Interface Recovery

```text
Checking SW-01
- Gi1/0/10 returned to operational state
- Gi1/0/1 remains operational
- SW-01 remains healthy
```

### Ticket Update

```text
Service Restored:

- Ethernet connection to BRANCH-PC1 reseated
- Gi1/0/10 returned to operational state
- BRANCH-PC1 regained network connectivity

Current Action:
Performing service validation.
```

---

# 8. Service Validation

The incident was **not closed immediately after the interface returned up**.

Connectivity was verified from the affected customer endpoint.

### BRANCH-PC1 Testing

```text
ping 10.10.10.1
ping 10.10.20.2
ping 8.8.8.8
ping google.com
```

### Results

```text
Default Gateway       REACHABLE
SW-01 Management      REACHABLE
External IP           REACHABLE
DNS Resolution        WORKING
Internet Access       WORKING
```

### Evidence — Endpoint Recovery

![BRANCH-PC1 Validation](screenshots/ticket01-07-endpoint-validation.png)

**What this proves:** Service was restored from the customer's endpoint, not merely at the switchport.

---

# 9. Monitoring Recovery

LibreNMS was reviewed again after service restoration.

### Dashboard Status

```text
Checking Dashboard
- SW-01 healthy
- Gi1/0/10 operational
- Previous alert cleared
- Monitoring returned to normal
```

### Evidence — LibreNMS Recovery

![LibreNMS Recovery](screenshots/ticket01-08-librenms-recovery.png)

**What this proves:** The monitoring platform independently confirmed recovery.

---

# 10. User Confirmation

Alex was contacted before closure.

```text
Calling site
- Alex confirmed BRANCH-PC1 is online
- Alex confirmed Internet access is restored
- No additional connectivity issues reported
- End call
```

This provided onsite confirmation in addition to the technical validation.

---

# 11. Resolution and Closure

## Root Cause

**Physical Layer connectivity failure**

The Ethernet cable serving BRANCH-PC1 was not fully seated, causing loss of physical link on **SW-01 Gi1/0/10**.

## Corrective Action

The Ethernet connection was reseated and secured.

No configuration changes were required on RTR-01 or SW-01.

## Final Ticket Update

```text
Resolution:

Connectivity loss was isolated to the physical Ethernet connection
between BRANCH-PC1 and SW-01 Gi1/0/10.

Corrective Action:
Ethernet cable was reseated and secured.

Validation:
- Gi1/0/10 operational
- BRANCH-PC1 network connectivity restored
- Default gateway reachable
- Internet connectivity verified
- DNS resolution verified
- LibreNMS returned to normal
- Alex Carter confirmed service restoration

Next Actions:
- Close TT
- Return Carolina Crest Bank — Branch 01 to proactive monitoring
```

**Final Status:** `Closed`

### Evidence — Closed Ticket

![Closed Spiceworks Ticket](screenshots/ticket01-09-ticket-closed.png)

**What this proves:** The incident was documented through resolution and formally closed.

---

# Incident Timeline

| Stage | Event |
|---|---|
| Baseline | Network confirmed healthy |
| Detection | LibreNMS detects connectivity condition |
| Ticket Creation | Incident opened in Spiceworks |
| Initial Triage | Router, switch, and uplink verified healthy |
| Investigation | Gi1/0/10 identified as not connected |
| Isolation | Gi1/0/10 mapped to BRANCH-PC1 |
| Site Contact | Alex Carter contacted |
| Root Cause | Loose Ethernet connection identified |
| Restoration | Ethernet cable reseated |
| Validation | BRANCH-PC1 connectivity verified |
| Monitoring Recovery | LibreNMS returns to normal |
| User Confirmation | Alex confirms service restoration |
| Closure | Ticket documented and closed |

---

# Troubleshooting Logic

```text
LibreNMS Alert
      ↓
Is RTR-01 reachable?
      ↓
     YES
      ↓
Is SW-01 reachable?
      ↓
     YES
      ↓
Is Gi1/0/1 operational?
      ↓
     YES
      ↓
Check access interfaces
      ↓
Gi1/0/10 NOT CONNECTED
      ↓
Map port to BRANCH-PC1
      ↓
Contact onsite representative
      ↓
Inspect physical connection
      ↓
Loose Ethernet cable found
      ↓
Reseat cable
      ↓
Gi1/0/10 returns UP
      ↓
Validate BRANCH-PC1
      ↓
Verify LibreNMS recovery
      ↓
Confirm with onsite contact
      ↓
Close ticket
```

---

# Skills Demonstrated

| Area | Skills |
|---|---|
| Monitoring | LibreNMS alerts, device/interface status, recovery validation |
| Cisco Networking | IOS interface troubleshooting, access-port investigation |
| Troubleshooting | Incident scoping, fault isolation, Layer 1 diagnosis |
| Ticketing | Spiceworks creation, updates, resolution, closure |
| Incident Response | Detection, triage, investigation, remediation, validation |
| Communication | Onsite coordination and service confirmation |

---

# Key Takeaways

- Monitoring alerts identify conditions, not necessarily root causes.
- Establishing incident scope early prevents unnecessary troubleshooting.
- Verifying healthy upstream infrastructure helped rule out a site-wide outage.
- Interface status provided the evidence needed to isolate the affected connection.
- Network documentation connected Gi1/0/10 to BRANCH-PC1.
- Physical Layer checks prevented unnecessary configuration changes.
- Service was validated from the customer endpoint before closing the incident.
- LibreNMS recovery provided independent confirmation that the condition had cleared.
- Spiceworks maintained a chronological record of the troubleshooting process.

---

# Evidence Index

| Screenshot | Evidence |
|---|---|
| `ticket01-01-healthy-baseline.png` | Healthy network before failure |
| `ticket01-02-librenms-alert.png` | Initial monitoring detection |
| `ticket01-03-ticket-created.png` | Initial Spiceworks incident |
| `ticket01-04-initial-triage.png` | Upstream infrastructure healthy |
| `ticket01-05-interface-investigation.png` | Gi1/0/10 failure identified |
| `ticket01-06-site-contact.png` | T1TS and physical issue documented |
| `ticket01-07-endpoint-validation.png` | BRANCH-PC1 connectivity restored |
| `ticket01-08-librenms-recovery.png` | Monitoring returned to normal |
| `ticket01-09-ticket-closed.png` | Completed and closed ticket |

---

## Final Status

```text
Incident Status     RESOLVED
Spiceworks Ticket   CLOSED
SW-01               HEALTHY
Gi1/0/10            OPERATIONAL
BRANCH-PC1          ONLINE
Internet Access     RESTORED
LibreNMS            NORMAL
Site                PROACTIVE MONITORING
```
