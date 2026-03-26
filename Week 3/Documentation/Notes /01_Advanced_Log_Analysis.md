# 01_Advanced_Log_Analysis

## Overview
Advanced log analysis is a key SOC activity used to detect suspicious behavior by reviewing, correlating, and enriching events from different log sources. In this task, Elastic Security was used to analyze authentication and network logs, create an anomaly-based rule, and add contextual information through GeoIP enrichment.

## Objective
The objective of this exercise was to:
- correlate logs from multiple sources to identify suspicious activity;
- create an anomaly detection rule in Elastic Security;
- enrich logs with location-based context;
- document findings in a structured SOC format.

## Tools Used
- Elastic Security
- Google Sheets / analyst notes
- Sample authentication and network logs

## Log Correlation
Log correlation was performed to connect repeated failed login attempts with outbound network activity. A failed login alone may not be serious, but repeated failures followed by suspicious outbound communication can indicate malicious behavior such as credential abuse or post-compromise activity.

### Correlation Table
| Timestamp | Event ID | Source IP | Destination IP | Notes |
|---|---:|---|---|---|
| 2025-08-18 12:00:00 | 4625 | 192.168.1.100 | 8.8.8.8 | Suspicious DNS request after failed login |
| 2025-08-18 12:01:12 | 4625 | 192.168.1.100 | 104.26.10.78 | Repeated failed login followed by outbound traffic |
| 2025-08-18 12:03:45 | 4625 | 192.168.1.100 | 198.51.100.23 | Possible callback behavior observed |
| 2025-08-18 12:05:10 | 5156 | 192.168.1.100 | 198.51.100.23 | Allowed outbound connection after multiple failures |

## Anomaly Detection Rule
A custom rule was created in Elastic Security to detect unusually high outbound data transfer.

**Rule Name:** High Volume Outbound Transfer  
**Condition:** `bytes_out > 1 MB in 1 minute`  
**Purpose:** Detect possible data exfiltration or abnormal outbound transfer activity.

The rule was tested using mock traffic and successfully generated an alert when the configured threshold was exceeded.

## GeoIP Enrichment
GeoIP enrichment was used to add country and city context to suspicious IP addresses. This helped distinguish normal traffic from unusual destinations and improved the quality of triage.

### GeoIP Enrichment Table
| IP Address | Country | City | Observation |
|---|---|---|---|
| 8.8.8.8 | United States | Mountain View | Public DNS traffic, reviewed due to timing |
| 104.26.10.78 | United States | San Francisco | External destination requiring validation |
| 198.51.100.23 | Reserved/Test Range | N/A | Mock suspicious destination used in lab |

## Findings
The analysis showed that repeated failed login attempts followed by outbound traffic from the same host were more suspicious when viewed together than as isolated events. GeoIP enrichment added context to the investigation, and the anomaly rule helped identify behavior that may indicate data exfiltration or unauthorized communication.

## SOC Value
This task demonstrated the practical value of advanced log analysis in a SOC environment. Correlation reduces false positives, enrichment improves analyst context, and anomaly detection helps uncover suspicious behavior that may not be visible through simple event review.

## Conclusion
This exercise successfully demonstrated advanced log analysis using Elastic Security. By correlating failed login events with outbound traffic, applying anomaly detection, and enriching IP data with GeoIP context, the investigation produced a clearer picture of suspicious activity and reflected an effective SOC analysis workflow.

## Screenshot Reference
**Suggested screenshot for this note:** `01_Elastic_Log_Correlation.png`  
Optional additional screenshots:
- `02_Elastic_Anomaly_Rule.png`
- `03_GeoIP_Enrichment.png`
