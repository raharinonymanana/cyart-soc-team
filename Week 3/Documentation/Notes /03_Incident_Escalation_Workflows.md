# 03_Incident_Escalation_Workflows

## Overview
Incident escalation workflows are essential in SOC operations because they ensure that serious alerts are handed over to the right level of expertise at the right time. In this task, a high-priority unauthorized access alert was reviewed, documented in TheHive, escalated to Tier 2, and summarized through a formal Situation Report (SITREP). A basic automation concept using Splunk Phantom was also included to show how escalation can be standardized.

## Objective
The objective of this exercise was to:
- understand the role of escalation in SOC operations;
- identify when an alert should move from Tier 1 to Tier 2;
- create a structured incident case in TheHive;
- draft a SITREP for technical and operational communication;
- understand how automation can support faster escalation.

## Tools Used
- TheHive
- Google Docs
- Splunk Phantom
- Wazuh / alert source context

## Escalation Scenario
A mock incident was created to simulate a high-priority unauthorized access event affecting a monitored server.

### Incident Details
| Field | Value |
|---|---|
| Title | Unauthorized Access on Server-Y |
| Detection Time | 2025-08-18 13:00 |
| Source IP | 192.168.1.200 |
| MITRE ATT&CK | T1078 – Valid Accounts |
| Initial Action | Server isolated and alert escalated |

This alert met escalation criteria because it involved unauthorized access to a server asset and indicated possible misuse of valid credentials.

## TheHive Case Creation
A case was created in TheHive to document the incident and preserve the initial analyst findings.

### TheHive Case Record
| Field | Value |
|---|---|
| Case Title | Unauthorized Access on Server-Y |
| Severity | High |
| Affected Asset | Server-Y |
| Detection Time | 2025-08-18 13:00 |
| Source IP | 192.168.1.200 |
| MITRE ATT&CK | T1078 – Valid Accounts |
| Analyst Action | Server isolated and escalation initiated |
| Escalation Status | Sent to Tier 2 |

TheHive provided a structured way to track the alert, preserve context, and support the handoff to the next investigation stage.

## Tier 2 Escalation Summary
A short escalation note was prepared for Tier 2 analysts.

A high-priority unauthorized access alert was detected on Server-Y on 2025-08-18 at 13:00. Initial review identified suspicious authentication activity associated with source IP 192.168.1.200 and mapped the behavior to MITRE ATT&CK technique T1078 (Valid Accounts). Due to the sensitivity of the affected server and the likelihood of credential misuse, the host was isolated as a containment measure. Basic triage was completed by Tier 1, and relevant alert metadata was documented in TheHive. Further investigation is required to determine account origin, scope of access, persistence mechanisms, and whether lateral movement or data access occurred following the unauthorized authentication event.

## Situation Report (SITREP)
A SITREP was drafted to summarize the incident clearly for stakeholders and responders.

### SITREP
**Title:** Unauthorized Access on Server-Y

**Incident Summary:**  
A high-priority unauthorized access event was detected on Server-Y at 2025-08-18 13:00. The suspicious activity originated from IP address 192.168.1.200 and aligned with MITRE ATT&CK technique T1078, indicating possible misuse of valid credentials.

**Affected Asset:**  
Server-Y

**Actions Taken:**  
The server was isolated, a case was created in TheHive, and the incident was escalated to Tier 2 for deeper investigation.

**Current Status:**  
Under investigation by Tier 2 analysts.

**Recommended Next Steps:**  
Validate the account involved, review authentication logs, assess lateral movement, and determine whether additional containment is required.

## Automation Concept
A basic Splunk Phantom automation workflow was considered to improve escalation speed and consistency.

### Example Automation Steps
1. Receive high-priority alert.
2. Check severity and asset type.
3. Enrich alert metadata.
4. Create case or ticket.
5. Auto-assign to Tier 2 queue.
6. Notify analyst team.

This workflow reduces manual routing delays and helps ensure that urgent alerts are handled consistently.

## Findings
The task showed that escalation is not only about moving an alert to another analyst. It also requires clear documentation, structured communication, and proper case handling. TheHive improved traceability, the SITREP supported communication, and the automation concept showed how the escalation process can be made faster and more reliable.

## SOC Value
This exercise demonstrated the operational value of good escalation workflows in a SOC. Effective escalation improves coordination, reduces delays, supports accountability, and ensures that serious incidents receive the level of investigation they require.

## Conclusion
This exercise successfully demonstrated incident escalation workflows through TheHive case creation, Tier 2 handoff, SITREP drafting, and automation planning. The task reflected a realistic SOC process where technical validation, communication, and structured documentation work together to support efficient incident response.

## Screenshot Reference
**Suggested screenshot for this note:** `06_TheHive_Case_Creation.png`  
Optional additional screenshots:
- `07_SITREP_Document.png`
- `08_Wazuh_Triage_Alert.png`
