# Incident 02

## Description

This incident documents a **controlled Tier 1 NOC WAN connectivity failure** within the NOC Operations Simulation Lab.

**Vertex Network Solutions** acts as the remote NOC responsible for monitoring and supporting **Carolina Crest Bank**. A controlled interruption was intentionally introduced on the branch WAN path to generate real LibreNMS alerts and simulate a site-level loss of connectivity.

This scenario is focused on **entry-level NOC / junior network operations responsibilities**, including alert review, incident triage, scope determination, WAN troubleshooting, ticket documentation, onsite coordination, ISP escalation, service restoration, recovery validation, and incident closure.

The incident was detected through LibreNMS on NOC-SRV01, documented in Spiceworks, investigated using both NOC-side and onsite troubleshooting, escalated through a **simulated ISP workflow**, and resolved after upstream service was restored.

## Objective

- Respond to a LibreNMS site-level outage alert
- Determine the scope of the incident
- Verify whether the issue is isolated to the branch or affecting the NOC environment
- Perform remote WAN troubleshooting
- Coordinate onsite Tier 1 troubleshooting
- Escalate to the ISP when local troubleshooting indicates an upstream issue
- Validate service recovery
- Document and close the incident in Spiceworks

## Tools Used

- **LibreNMS** - Monitoring and alerting
- **Spiceworks** - Ticketing Software
- **NOC-SRV01** - Ubuntu Linux VM
- **RTR-01** - Cisco 2911 
- **SW-01** - Cisco Catalyst 2960
- **BRANCH-PC1** - Windows endpoint

## Incident Summary

LibreNMS generated four critical alerts after remote connectivity to **Carolina Crest Bank - Branch 01** was lost. RTR-01 and SW-01 became unreachable through both SNMP and ICMP.

Initial triage confirmed that **NOC-SRV01 remained online with Internet connectivity**, while RTR-01 and SW-01 were unreachable from the NOC. Onsite testing from BRANCH-PC1 confirmed that the local LAN and default gateway were still reachable, but Internet and DNS connectivity were unavailable.

Onsite troubleshooting verified that the branch network equipment remained powered and the WAN connection was checked. The AT&T gateway showed an abnormal service/status condition, leading to a simulated ISP escalation.

The simulated provider reported an upstream service interruption with an ETR of 60 minutes. After service was restored, RTR-01 and SW-01 returned online in LibreNMS, monitoring alarms cleared, branch connectivity was confirmed, and the Spiceworks ticket was closed.

## Incident Details

- **Incident ID:** INC-02
- **Title:** Branch WAN / ISP Connectivity Incident
- **Severity:** Sev 2
- **Status:** Resolved
- **Detection Method:** LibreNMS alert
- **Affected Site:** Carolina Crest Bank - Branch 01
- **Equipment in Alarm:** RTR-01 / SW-01
- **Affected Services:** Branch WAN, Internet, and remote reachability
- **Environment:** Lab / simulated NOC workflow

## Healthy Baseline

Before introducing the controlled failure, the branch network was verified in its normal operational state.

This baseline provided a known-good reference before the incident was introduced.

LibreNMS confirmed:

- RTR-01 online
- SW-01 online
- No active alerts
- Branch WAN connectivity operational
- BRANCH-PC1 Internet connectivity operational

**RTR-01 and SW-01 Online**

<img width="1415" height="409" alt="image" src="https://github.com/user-attachments/assets/92f42069-c1db-4b37-b360-f46eae0a1256" />

---

## 1. Detection & Alerting

The branch WAN connection was intentionally interrupted to create the controlled incident.

LibreNMS detected the loss of remote reachability and generated four **Critical** alerts associated with RTR-01 and SW-01.

The alerts reported:

- RTR-01 - Device Down (SNMP unreachable)
- SW-01 - Device Down (SNMP unreachable)
- RTR-01 - Device Down due to no ICMP response
- SW-01 - Device Down due to no ICMP response

### Alert Detection

<img width="1886" height="466" alt="image" src="https://github.com/user-attachments/assets/9c88f594-86a9-4aec-81b7-24d5d6dd63b5" />

---

### Unhealthy Device State

Both monitored branch devices appeared unreachable from the Vertex Network Solutions NOC.

<img width="1415" height="416" alt="image" src="https://github.com/user-attachments/assets/fdc04844-3db8-4c9c-9738-077d7be21065" />

---

[← Back to Incdient 01](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%2001.md)

[← Back to Incdient Response & Ticketing](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%20Response%20%26%20Ticketing.md)
