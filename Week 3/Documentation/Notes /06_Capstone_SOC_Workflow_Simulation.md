# 06_Capstone_SOC_Workflow_Simulation

## Overview
This capstone task simulated a complete SOC workflow from attack detection to final reporting. It combined attack simulation, alert monitoring, triage, containment, escalation, and communication into one end-to-end exercise. The goal was to demonstrate how multiple SOC tools and processes work together during a realistic security incident.

## Objective
The objective of this exercise was to:
- simulate a controlled attack in a lab environment;
- detect malicious activity using Wazuh;
- triage the alert and identify the related MITRE ATT&CK technique;
- apply containment actions using CrowdSec and host isolation;
- escalate the incident to Tier 2 using TheHive;
- document the incident in both technical and non-technical formats.

## Tools Used
- Metasploit
- Wazuh
- CrowdSec
- TheHive
- Google Docs

## Incident Scenario
A controlled attack simulation was performed against a vulnerable lab system. The activity was detected by Wazuh and reviewed as part of the SOC workflow.

### Detection Record
| Timestamp | Source IP | Alert Description | MITRE Technique |
|---|---|---|---|
| 2025-08-18 14:00:00 | 192.168.1.101 | Samba exploit | T1210 |

The alert was treated as high priority because it represented exploitation of a remote service and indicated active malicious behavior in the lab environment.

## Detection and Triage
Wazuh generated an alert for the suspicious activity, and the alert was reviewed to confirm that it matched the attack scenario.

### Triage Observations
- the activity matched a known exploitation pattern;
- the source IP belonged to the attacking system in the lab;
- the target system was vulnerable;
- the behavior aligned with **MITRE ATT&CK T1210 – Exploitation of Remote Services**.

Based on these observations, the alert was confirmed as malicious in the lab scenario and moved to response actions.

## Response and Containment
Once the alert was validated, the following containment actions were taken:
1. the affected virtual machine was isolated from the network;
2. the attacker IP address was blocked using CrowdSec.

### Containment Record
| Action | Target | Result | Verification |
|---|---|---|---|
| Host Isolation | Vulnerable VM | Network exposure reduced | Host removed from active communication path |
| CrowdSec IP Block | 192.168.1.101 | Attacker IP denied | Ping test failed after block |

A ping test was used to confirm that the IP block was effective. This verified that the attacker host could no longer communicate with the target.

## Escalation to Tier 2
After containment, the incident was escalated to Tier 2 in TheHive for deeper investigation.

### Tier 2 Case Summary
A high-priority exploitation alert was detected by Wazuh on 2025-08-18 at 14:00:00, identifying a Samba exploit attempt originating from source IP 192.168.1.101. The behavior was mapped to MITRE ATT&CK technique T1210, indicating exploitation of a remote service. Initial triage confirmed that the activity was malicious within the lab scenario and affected a vulnerable target system. Immediate response actions included isolating the impacted virtual machine and blocking the attacker IP through CrowdSec. A ping verification test confirmed successful containment. The incident was then escalated to Tier 2 in TheHive for scope validation, forensic review, and assessment of any follow-on compromise.

## Incident Report
A short formal incident report was created to summarize the event.

### Technical Summary
**Executive Summary:**  
A simulated exploitation incident was conducted in the SOC lab to test end-to-end detection and response capability. Wazuh detected a Samba exploit from IP address 192.168.1.101 and mapped the activity to MITRE ATT&CK T1210. The alert was triaged as high priority and containment actions were applied using host isolation and CrowdSec IP blocking.

**Timeline:**  
- 14:00:00 – Wazuh generated the exploit alert  
- 14:03 – Analyst reviewed and confirmed malicious activity  
- 14:07 – The affected virtual machine was isolated  
- 14:10 – CrowdSec block rule was applied  
- 14:12 – Ping verification confirmed containment  
- 14:18 – TheHive case was created and escalated to Tier 2  

**Recommendations:**  
Preserve logs and host evidence, continue Tier 2 review, improve detection coverage for remote service exploitation, and maintain tested containment workflows.

## Non-Technical Briefing
A short non-technical briefing was prepared for management.

A simulated cyberattack was conducted in the lab to test the security team’s detection and response process. The monitoring platform detected the attack quickly, the team confirmed it as malicious in the test environment, and immediate steps were taken to isolate the affected machine and block the attacker’s IP address. The case was then escalated for deeper review. The exercise showed that the detection, response, and reporting workflow is functioning effectively and can support structured incident handling.

## Findings
The capstone showed that an effective SOC workflow depends on coordination between tools, analysts, and documentation. Wazuh handled detection, CrowdSec supported containment, TheHive supported escalation, and formal reporting preserved the results. The task demonstrated that detection alone is not enough; it must be followed by triage, response, escalation, and communication.

## SOC Value
This exercise demonstrated the operational value of an end-to-end SOC workflow. It showed how incidents can be detected, investigated, contained, escalated, and reported through a structured process. This type of capstone also helps measure readiness and validate whether the tools and procedures work together effectively.

## Conclusion
This capstone successfully demonstrated a complete SOC workflow simulation. A controlled exploitation attempt was detected by Wazuh, triaged as high priority, contained through host isolation and CrowdSec blocking, escalated to Tier 2 in TheHive, and documented through technical and management reporting. The task reflected a realistic and well-structured SOC response process.

## Screenshot Reference
**Suggested screenshot for this note:** `12_Metasploit_Attack_Simulation.png`  
Optional additional screenshots:
- `13_Wazuh_Detection.png`
- `14_CrowdSec_Block_Action.png`
- `15_Final_Report_or_Briefing.png`
