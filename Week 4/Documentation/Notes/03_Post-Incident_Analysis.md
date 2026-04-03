# Week 4 – Post-Incident Analysis

## 1. Objective
The objective of this activity was to perform a post-incident review on a suspicious authentication-related alert observed in the lab environment. The Week 4 task requires root cause analysis, lessons learned, and basic SOC metric calculation such as Mean Time to Detect (MTTD) and Mean Time to Respond (MTTR). :contentReference[oaicite:1]{index=1}

## 2. Incident Selected
For this activity, a previously observed lab alert from Week 3 was reused as the incident under review. The incident involved failed authentication / failed SSH-related activity associated with the Kali lab machine.

### Core Incident Context
- Incident type: Failed authentication / failed SSH attempt
- Source IP: `192.168.56.102`
- Source system: Kali attacker machine
- Detection source: Wazuh
- Example reviewed timestamp: `Mar 31, 2026 @ 23:00:21.969`
- General alert context: Authentication failure / unauthorized access attempt in the lab environment

This alert was suitable for post-incident analysis because it represented a realistic unauthorized access attempt that could be reviewed from detection, triage, response, and improvement perspectives.

## 3. Incident Summary
The incident began when repeated authentication-related activity from the Kali source IP `192.168.56.102` generated an alert in Wazuh. The event indicated unauthorized or failed access behavior against a reachable service in the lab. The alert was reviewed by the analyst, linked to attacker activity from the Kali system, and later reused as the basis for SOAR playbook and post-incident analysis. Although the incident occurred in a controlled lab environment, it simulated a realistic brute-force or unauthorized access attempt that required analyst validation and containment planning.

## 4. Timeline of the Incident
The following simplified timeline was used for analysis:

| Time | Event |
|---|---|
| 23:00 | Failed authentication / failed SSH attempt initiated from Kali |
| 23:01 | Wazuh generated the alert |
| 23:05 | Analyst reviewed the alert and validated the source context |
| 23:10 | Incident categorized as suspicious authentication activity |
| 23:15 | Escalation / containment logic prepared |
| 23:20 | Post-incident analysis initiated |

This timeline is a lab-based reconstruction used for RCA and metric calculation.

## 5. Root Cause Analysis – 5 Whys

| Why Question | Answer |
|---|---|
| Why was the alert generated? | Because failed authentication / failed SSH activity was detected from the Kali source IP. |
| Why did the authentication failure happen? | Because the attacker machine attempted access to the target service using invalid or unauthorized credentials. |
| Why was the service reachable for such attempts? | Because the service was exposed and reachable in the controlled lab environment for testing purposes. |
| Why did the activity require investigation? | Because repeated authentication failures may indicate brute-force attempts, unauthorized access, or reconnaissance activity. |
| Why should the response process be improved? | Because faster validation and automated containment would reduce analyst effort and improve incident response consistency. |

## 6. Root Cause Statement
The incident was caused by unauthorized authentication attempts from the Kali attacker machine against a reachable lab service, combined with an initially manual response workflow that required analyst validation before containment or escalation could be performed.

## 7. Fishbone Diagram Content
The following categories and causes were identified for the post-incident Fishbone analysis:

| Category | Cause |
|---|---|
| People | Analyst validation was manual |
| Process | No fully automated initial response for repeated authentication alerts |
| Technology | Reachable authentication service in the lab environment |
| Environment | Attacker and target systems were intentionally placed in the same controlled network |
| Monitoring / Detection | Detection worked correctly, but triage still required manual review |
| Response | Containment was not immediately automated at the first detection stage |

These elements can be converted into a Fishbone Diagram in Draw.io for the final submission.

## 8. Metrics Calculation
For the purpose of this lab analysis, the following realistic metric values were used based on the reconstructed timeline:

- Incident start: `23:00`
- Detection by Wazuh: `23:01`
- Analyst review began: `23:05`
- Response / containment preparation: `23:15`

### Calculated Metrics
- **MTTD (Mean Time to Detect)** = 1 minute
- **MTTR (Mean Time to Respond)** = 14 minutes

### Metrics Table

| Metric | Value | Interpretation |
|---|---|---|
| MTTD | 1 minute | Wazuh detected the suspicious activity quickly |
| MTTR | 14 minutes | Response required analyst validation and follow-up actions before containment logic was completed |
| Dwell Time | 1 minute | Time between incident start and detection in this lab reconstruction |
| False Positive Rate | Not calculated | Single-incident lab exercise only |

## 9. Lessons Learned

| Lesson Learned | Improvement Needed |
|---|---|
| Wazuh detected the alert successfully | Keep authentication-related alert coverage enabled |
| The source context was understandable | Preserve host/IP enrichment in alert views |
| Validation still relied on analyst review | Add stronger IOC enrichment and contextual automation |
| Response was not fully automatic | Integrate case creation and blocking into a playbook |
| Repeated alert categories can be standardized | Build a repeatable workflow for authentication-based incidents |

## 10. Improvement Recommendations
Based on the post-incident analysis, the following improvements are recommended:

1. Improve automated triage for repeated failed authentication alerts.
2. Integrate alert escalation more directly with TheHive case creation.
3. Add automated source IP enrichment or reputation checking where appropriate.
4. Connect containment actions, such as CrowdSec blocking, more tightly to alert confirmation.
5. Standardize post-incident review templates for recurring attack categories such as failed logins, suspicious remote access attempts, and brute-force behavior.

## 11. 50-Word Post-Incident Summary
A post-incident review was conducted on a failed authentication alert linked to Kali source IP `192.168.56.102`. The analysis showed that Wazuh detected the event quickly, but validation and response remained partly manual. Future improvements should focus on faster enrichment, standardized playbooks, and tighter integration between detection, triage, and containment. :contentReference[oaicite:2]{index=2}

## 12. Practical Interpretation
This post-incident analysis demonstrates that even a relatively simple failed authentication event can be used to evaluate SOC workflow maturity. The incident was detected correctly, understood in context, and suitable for escalation into a case-handling and containment workflow. The main weakness was not detection, but the dependency on manual validation and response coordination. This makes the incident useful not only as a security event, but also as a process improvement case study.
