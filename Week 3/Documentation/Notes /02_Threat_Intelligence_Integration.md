# 02_Threat_Intelligence_Integration

## Overview
Threat intelligence integration improves SOC operations by adding external context to alerts and indicators. Instead of reviewing events in isolation, analysts can validate suspicious IPs, domains, and other IOCs against known threat intelligence sources. In this task, Wazuh was integrated with AlienVault OTX to enrich alerts and support intelligence-driven threat hunting.

## Objective
The objective of this exercise was to:
- integrate a threat intelligence source into Wazuh;
- enrich alerts with IOC reputation data;
- validate suspicious indicators using AlienVault OTX;
- perform a threat hunt related to suspicious account activity;
- document findings in a structured SOC format.

## Tools Used
- Wazuh
- AlienVault OTX
- TheHive
- Google Sheets / analyst notes

## Threat Feed Integration
An AlienVault OTX threat feed was integrated into Wazuh to provide external IOC context for alerts. This allowed suspicious IP addresses to be matched against known malicious infrastructure and improved the accuracy of triage.

A mock suspicious IP, `192.168.1.100`, was used to test the enrichment workflow and confirm that the feed was functioning correctly.

## Alert Enrichment
A Wazuh alert containing the suspicious IP was reviewed and enriched using OTX intelligence. The reputation result showed that the indicator was associated with malicious activity.

### Enrichment Record
| Alert ID | IP | Reputation | Notes |
|---|---|---|---|
| 003 | 192.168.1.100 | Malicious (OTX) | Linked to C2 server |

The enrichment process increased analyst confidence that the alert required further attention and should not be treated as a low-priority event.

## Threat Hunting Activity
A basic threat hunting query was used to identify suspicious user account activity related to valid account misuse.

**Sample Query:**  
`user.name != "system"`

The goal of this query was to filter out normal system activity and highlight potentially suspicious authentication events involving non-system accounts. This supports investigation of **MITRE ATT&CK T1078 – Valid Accounts**.

## Findings
The exercise showed that threat intelligence enrichment improves alert quality by giving additional meaning to suspicious indicators. The OTX result identified the IP as malicious, while the hunting query helped isolate suspicious account activity that may indicate credential misuse or unauthorized access.

## SOC Value
This task demonstrated how threat intelligence helps SOC analysts prioritize alerts more accurately, reduce manual validation work, and perform more targeted investigations. Intelligence integration supports faster triage, better escalation decisions, and stronger incident documentation.

## Conclusion
This exercise successfully demonstrated threat intelligence integration using Wazuh and AlienVault OTX. By enriching a suspicious alert with IOC reputation data and applying intelligence-driven hunting for T1078, the task showed how external threat context strengthens detection, triage, and investigation in SOC operations.

## Screenshot Reference
**Suggested screenshot for this note:** `04_OTX_Wazuh_Integration.png`  
Optional additional screenshots:
- `05_Threat_Hunting_Query.png`
- `09_VirusTotal_OTX_Validation.png`
