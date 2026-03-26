# 04_Alert_Triage_with_Threat_Intelligence

## Overview
Alert triage is a critical SOC process used to determine whether a detection is benign, suspicious, or requires escalation. In this task, a mock Wazuh alert for suspicious PowerShell execution was reviewed and validated using external threat intelligence sources. The activity demonstrated how analysts combine alert context and IOC validation to make better triage decisions.

## Objective
The objective of this exercise was to:
- analyze a suspicious Wazuh alert;
- assign an appropriate priority and status;
- validate the associated IOC using VirusTotal and AlienVault OTX;
- document the triage result in a clear and structured format;
- support further escalation if required.

## Tools Used
- Wazuh
- VirusTotal
- AlienVault OTX
- Google Sheets / analyst notes

## Alert Scenario
A mock alert was generated in Wazuh to simulate suspicious PowerShell activity on a monitored host.

### Alert Record
| Alert ID | Description | Source IP | Priority | Status |
|---|---|---|---|---|
| 004 | PowerShell Execution | 192.168.1.101 | High | Open |

The alert was initially treated as high priority because PowerShell is a legitimate administrative tool that is frequently abused by attackers for execution, persistence, and post-exploitation actions.

## Initial Triage
The first stage of triage focused on reviewing the alert details in Wazuh. The following points were considered:
- the description and rule context of the alert;
- the host involved;
- whether the behavior matched expected administrative activity;
- the possible connection to attacker techniques or suspicious scripts;
- whether the source IP showed signs of malicious reputation.

The alert remained open during this stage because more context was needed before making a final decision.

## IOC Validation
The source IP was checked against VirusTotal and AlienVault OTX to determine whether it had any known malicious or suspicious reputation.

### IOC Validation Record
| IOC Type | IOC Value | Source Checked | Result | Analyst Interpretation |
|---|---|---|---|---|
| IP | 192.168.1.101 | VirusTotal | Suspicious / flagged in test context | Requires further review |
| IP | 192.168.1.101 | AlienVault OTX | Associated with suspicious activity in lab context | Supports elevated concern |

The validation results increased confidence that the alert should not be dismissed as harmless. The combination of suspicious PowerShell execution and intelligence-supported IOC context justified continued review.

## Triage Decision
After reviewing the alert and validating the IOC, the final decision was to keep the alert active for further investigation.

### Final Triage Outcome
| Alert ID | Final Priority | Final Status | Decision |
|---|---|---|---|
| 004 | High | Open / Under Review | Keep active and prepare for escalation if needed |

The alert was not closed because the observed behavior and IOC validation suggested a meaningful risk that required ongoing investigation.

## Findings
The triage process showed that PowerShell alerts must be evaluated carefully because the tool is commonly used by both administrators and attackers. Threat intelligence validation helped reduce ambiguity and supported a more evidence-based decision. The task also showed the importance of documenting triage clearly so that a higher-tier analyst can continue the investigation without repeating the initial work.

## SOC Value
This exercise demonstrated the value of combining Wazuh alert review with external intelligence sources. This approach improves analyst confidence, supports better prioritization, reduces false positives, and helps ensure that suspicious activity receives the right level of attention.

## Conclusion
This task successfully demonstrated alert triage using Wazuh, VirusTotal, and AlienVault OTX. A suspicious PowerShell alert was reviewed, enriched with IOC reputation data, and kept open for further investigation based on the combined evidence. The workflow reflected good SOC practice for structured and intelligence-supported triage.

## Screenshot Reference
**Suggested screenshot for this note:** `08_Wazuh_Triage_Alert.png`  
Optional additional screenshots:
- `09_VirusTotal_OTX_Validation.png`
