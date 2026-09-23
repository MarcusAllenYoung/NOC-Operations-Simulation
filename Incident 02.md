# Incident 02

## Description

This incident documents a **controlled Tier 1 NOC WAN connectivity failure** within the NOC Operations Simulation Lab.

**Vertex Network Solutions** acts as the remote NOC responsible for monitoring and supporting **Carolina Crest Bank**. A controlled interruption was intentionally introduced on the branch WAN path to generate real LibreNMS alerts and simulate a site-level loss of connectivity.

This scenario is focused on **entry-level NOC / junior network operations responsibilities**, including alert review, incident triage, scope determination, WAN troubleshooting, ticket documentation, onsite coordination, ISP escalation, service restoration, recovery validation, and incident closure.

The incident was detected through LibreNMS on NOC-SRV01, documented in Spiceworks, investigated using both NOC-side and onsite troubleshooting, escalated through a **simulated ISP workflow**, and resolved after upstream service was restored.

## Objective

- Practice the Tier 1 NOC incident response lifecycle for a site-level WAN outage
- Review and interpret multiple monitoring alerts
- Determine incident scope and isolate the affected environment
- Distinguish between local LAN, NOC, and upstream provider issues
- Coordinate onsite troubleshooting and ISP escalation
- Document troubleshooting, RFO, ETR, and recovery actions
- Validate monitoring and service recovery before closure
- Complete the incident lifecycle from detection through resolution

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

## 2. Logging & Ownership

A network incident was created in Spiceworks and assigned to the NOC technician for investigation.

The ticket documented:

- Carolina Crest Bank
- Site CCB-BR01
- Severity 2
- RTR-01 / SW-01 as equipment in alarm
- Four critical LibreNMS alerts
- High priority
- Network category

**Spiceworks Ticket Created**

<img width="595" height="679" alt="image" src="https://github.com/user-attachments/assets/d7e46876-d165-4a1f-a9fd-f92deac5329d" />

<img width="1510" height="437" alt="image" src="https://github.com/user-attachments/assets/80c23c9f-56d0-4666-b5c8-17771e5b7f43" />

---

## 3. Triage & Prioritization

The first NOC action was to review the LibreNMS dashboard and determine whether the alerts represented a monitoring issue or a larger branch connectivity outage.

Initial findings:

- RTR-01 unreachable
- SW-01 unreachable
- Four active critical alerts
- SNMP polling unavailable
- ICMP reachability unavailable

The incident was classified as **Sev 2 / High priority** due to the loss of remote connectivity to multiple devices at Carolina Crest Bank - Branch 01.

**Ticket Documentation**

<img width="1498" height="257" alt="image" src="https://github.com/user-attachments/assets/8a559552-18f0-4bbb-8c2b-0eda1e7f38d6" />

---

## 4. Impact Assessment

NOC-SRV01 was used to determine whether the outage was affecting the Vertex Network Solutions environment or isolated to Carolina Crest Bank.

Connectivity tests were performed from the NOC to both public Internet resources and the monitored branch devices.

The results confirmed:

- NOC-SRV01 retained Internet connectivity
- 8.8.8.8 reachable from NOC-SRV01 with 0% packet loss
- google.com reachable from NOC-SRV01 with 0% packet loss
- RTR-01 unreachable from NOC-SRV01
- SW-01 unreachable from NOC-SRV01

This confirmed that Vertex Network Solutions remained operational while remote connectivity to Carolina Crest Bank was unavailable.

The available evidence indicated that the outage was isolated to the branch and a site-level WAN issue was suspected.

**Ticket Documentation**

<img width="1490" height="359" alt="image" src="https://github.com/user-attachments/assets/a9c4e934-c65e-4fd0-bbdd-9c3758b9b6e8" />

---

## 5. Investigation & Diagnosis

Because RTR-01 and SW-01 were unreachable from the NOC, the NOC contacted **Carolina Crest Bank** and continued troubleshooting with onsite representative **Alex Carter**.

Alex was asked to perform connectivity tests from BRANCH-PC1 to determine whether the local LAN was still operational.

The results confirmed:

- Default gateway 10.10.10.1 reachable with 0% packet loss
- 8.8.8.8 unreachable
- google.com unable to resolve

This showed that BRANCH-PC1 could still reach the local gateway while Internet and DNS connectivity were unavailable, narrowing the failure beyond the local LAN.

#### BRANCH-PC1 Connectivity Tests

**Default Gateway Reachable**

<img width="1883" height="343" alt="image" src="https://github.com/user-attachments/assets/c799bb70-4e08-4aa9-b252-39fcbfdc3bba" />

---

**Internet / DNS Connectivity Unavailable**

<img width="1826" height="284" alt="image" src="https://github.com/user-attachments/assets/7db19c1a-3405-444a-8b53-97195449772c" />

---

<img width="1186" height="113" alt="image" src="https://github.com/user-attachments/assets/8bf83694-9bb1-4a85-80f2-ac2317206e22" />

---

**Ticket Documentation**

<img width="1490" height="449" alt="image" src="https://github.com/user-attachments/assets/329f2e2c-f84f-4eee-8cff-8cb92c60fd6b" />

---

## Investigation & Diagnosis Continued

Troubleshooting continued during the call with Alex.

The NOC asked Alex to inspect the branch WAN equipment.

The onsite check confirmed:

- RTR-01 powered on
- SW-01 powered on
- AT&T gateway powered on
- WAN Ethernet connection checked
- AT&T gateway service/status light **blinking red rapidly**

According to the AT&T gateway status reference used in the lab, a rapidly blinking red light indicates a network service issue that may require AT&T intervention.

The abnormal gateway status, combined with the loss of Internet and DNS connectivity, indicated a possible upstream provider issue and justified escalating the incident to the ISP.

**Ticket Documentation**

<img width="1487" height="400" alt="image" src="https://github.com/user-attachments/assets/5cce9041-b6dd-48eb-9204-4b39da57b7c3" />

---

## 6. Resolution & Recovery

Based on the onsite findings, the incident was escalated to the ISP for service-status verification.

The NOC provided AT&T with the troubleshooting results collected from both the NOC and the branch site, including:

- Local branch LAN verified operational
- RTR-01, SW-01, and AT&T gateway confirmed powered on
- WAN Ethernet connection checked
- AT&T gateway service/status light blinking red rapidly
- Branch Internet and DNS connectivity unavailable

The simulated ISP response reported:

- Provider service outage
- RFO: Upstream service interruption
- ETR: 60 minutes

The NOC then contacted **Alex Carter** and informed the site of the provider outage and estimated restoration time.

**Ticket Documentation**

<img width="1481" height="487" alt="image" src="https://github.com/user-attachments/assets/c5e9a9cb-0db1-428c-af8a-6ca9f1abb744" />

---

After the simulated provider recovery, remote connectivity to the branch was restored.

LibreNMS recorded recovery events for both monitored devices:

- RTR-01 returned online
- SW-01 returned online
- SNMP polling resumed
- ICMP reachability returned
- Active alerts cleared

---

**LibreNMS recorded the recovery and cleared the active alerts. State Goes from Red to Green.**

<img width="1876" height="656" alt="image" src="https://github.com/user-attachments/assets/d1993b4d-a93a-4275-addd-a3228ffdfc3d" />

---

**RTR-01 and SW-01 returned Online**

<img width="1415" height="409" alt="image" src="https://github.com/user-attachments/assets/134daa77-0e21-4ec5-9da3-9d7978bc688a" />

---

## 7. Validation & Closure

After LibreNMS showed the branch devices had recovered, the NOC validated that remote connectivity had returned.

Validation confirmed:

- RTR-01 reachable from the NOC
- SW-01 reachable from the NOC
- LibreNMS alarms cleared
- Remote monitoring restored

The NOC then contacted **Alex Carter** to confirm service from the branch.

Alex confirmed:

- BRANCH-PC1 Internet connectivity restored
- No additional connectivity issues reported

The final recovery results were documented in Spiceworks.

<img width="1480" height="592" alt="image" src="https://github.com/user-attachments/assets/69041c71-3c90-4723-b864-0cb2c1f9adfc" />

---

## 8. Post-Incident Review

**Root Cause:** Controlled interruption of the WAN connection between RTR-01 and the upstream AT&T gateway.

**Simulated Operational Cause:** Upstream ISP service interruption affecting Carolina Crest Bank.

The incident demonstrated the importance of separating local LAN health, NOC connectivity, and upstream WAN availability when troubleshooting a site-level outage.

NOC-SRV01 remained connected to the Internet throughout the incident while RTR-01 and SW-01 became unreachable from the NOC. Onsite testing confirmed that BRANCH-PC1 could still reach its local default gateway, while Internet and DNS connectivity were unavailable.

The combination of a healthy local LAN, powered branch equipment, verified WAN cabling, and the abnormal AT&T gateway service/status indicator helped narrow the failure domain toward the upstream provider before escalation.

### Lessons Learned

- Verify the NOC environment before assuming the monitoring platform is the source of the outage
- Determine the incident scope before beginning deeper troubleshooting
- A reachable default gateway does not confirm Internet connectivity
- Compare local LAN health against upstream WAN availability
- Use onsite personnel when remote access to the affected site is unavailable
- Check branch WAN equipment and provider gateway status before escalating
- Document troubleshooting evidence before contacting the ISP
- Record provider RFO and ETR information in the incident ticket
- Confirm monitoring recovery, device reachability, and site connectivity before closing the ticket

**Final Status:** Resolved — provider service restored, monitoring returned to normal, branch connectivity confirmed, ticket closed, and Carolina Crest Bank - Branch 01 returned to proactive monitoring.


[← Back to Incdient 01](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%2001.md)

[← Back to Incdient Response & Ticketing](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%20Response%20%26%20Ticketing.md)
