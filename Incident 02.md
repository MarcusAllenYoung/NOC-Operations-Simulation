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
- **NOC-SRV01** - Ubuntu monitoring and management server
- **RTR-01** - Cisco 2911 branch router
- **SW-01** - Cisco Catalyst 2960
- **BRANCH-PC1** - Windows endpoint
- **AT&T Gateway** - Upstream WAN gateway

[← Back to Incdient 01](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%2001.md)

[← Back to Incdient Response & Ticketing](https://github.com/MarcusAllenYoung/NOC-Operations-Simulation/blob/main/Incident%20Response%20%26%20Ticketing.md)
