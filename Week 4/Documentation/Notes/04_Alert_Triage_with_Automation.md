# Week 4 – Alert Triage with Automation

## 1. Objective
The objective of this activity was to triage a suspicious alert in Wazuh, validate the associated indicator, assign priority and status, and document the response path in a case-management workflow. The Week 4 task requires alert analysis in Wazuh, threat-intelligence validation, and a short documented triage result. :contentReference[oaicite:1]{index=1}

## 2. Tools Used
- Wazuh
- TheHive
- VirusTotal / AlienVault OTX
- Manual analyst triage workflow

## 3. Lab Adaptation
The original task example uses a mock alert such as a suspicious file download and suggests automated validation with VirusTotal. In this lab, a previously observed real alert from the controlled environment was reused for the triage workflow. This kept the activity aligned with the task while grounding it in an actual lab finding. :contentReference[oaicite:2]{index=2}

## 4. Alert Selected
The alert selected for triage was a previously observed authentication-related alert generated in Wazuh during the lab.

### Alert Context
- Alert type: Failed authentication / failed SSH-related activity
- Source IP: `192.168.56.102`
- Source system: Kali attacker machine
- Detection source: Wazuh
- Example reviewed timestamp: `Mar 31, 2026 @ 23:00:21.969`

This alert was chosen because it contained a clear source IP, represented suspicious access behavior, and could be realistically escalated for case tracking and response preparation.

## 5. Triage Goal
The goal of the triage process was to determine:
- whether the alert was relevant,
- what priority should be assigned,
- whether the source IP should be retained as suspicious,
- and whether the event should be escalated for follow-up handling.

## 6. Initial Alert Review
The alert was reviewed in Wazuh and interpreted as suspicious authentication activity. Because the source IP `192.168.56.102` belonged to the Kali system used as the attacker machine in the lab, the event was not treated as benign background noise. Instead, it was considered a meaningful signal of unauthorized access behavior in the controlled environment.

## 7. Indicator Validation
The source indicator selected for validation was:

`192.168.56.102`

Threat-intelligence validation was attempted using public reputation lookup logic. Because this IP belongs to a private/internal lab range, no public intelligence result was expected. This outcome was considered normal.

### Validation Result
- IOC checked: `192.168.56.102`
- Result: No public intelligence / private internal lab IP
- Analyst interpretation: Retained as suspicious due to known Kali attacker role in the lab

This means the alert remained relevant even without public threat-intelligence enrichment, because the source was already known in the lab attack context.

## 8. Triage Classification
The alert was classified as follows:

- Category: Suspicious authentication activity
- Priority: High
- Status: Open / Under Investigation

### Reason for Priority
The alert was assigned **High** priority because repeated or suspicious authentication failures can indicate brute-force behavior, unauthorized access attempts, or reconnaissance. In this case, the known lab attacker source further increased the relevance of the event.

## 9. Triage Record Table

| Alert ID | Description | Source IP | Priority | Status |
|---|---|---|---|---|
| 005 | Failed authentication / failed SSH-related alert | 192.168.56.102 | High | Open |

This table represents the core triage output for the activity and matches the documentation style expected by the Week 4 task. :contentReference[oaicite:3]{index=3}

## 10. Case Handling Logic
A case-management workflow was prepared around this alert using TheHive. The alert was suitable for escalation because it contained:
- a clear source indicator,
- a defined suspicious behavior type,
- a known lab attacker context,
- and a reasonable need for follow-up investigation or containment planning.

### Example Case Content
**Title:**  
`[High] Failed Authentication Alert from Kali Source IP`

**Severity:**  
High

**Description:**  
A Wazuh authentication-related alert involving failed access behavior from Kali source IP `192.168.56.102` was triaged during the Week 4 exercise. The alert was classified as suspicious authentication activity and retained for investigation because the source system was the known attacker machine in the lab.

**Observables:**  
- Source IP: `192.168.56.102`
- Source system: Kali
- Alert source: Wazuh
- Alert type: Failed authentication / failed SSH-related activity

## 11. Analyst Triage Note
The following note was prepared for the triage record:

> Initial triage completed. The Wazuh alert was reviewed and linked to source IP `192.168.56.102`, identified as the Kali attacker system used in the lab. Threat-intelligence enrichment did not provide a public result because the IP belongs to a private/internal range. The alert remains suspicious due to lab context and authentication-failure behavior and is suitable for escalation and response testing.

## 12. Triage Decision
The final triage decision was:

- **Escalate and retain for response testing**

### Rationale
The alert represented suspicious authentication activity from a known attacker host in the lab. Even though the IP was private and could not be enriched through public intelligence, the alert was still actionable because the source context was already known and relevant.

## 13. 50-Word Triage Summary
A Wazuh authentication-related alert from Kali source IP `192.168.56.102` was triaged as suspicious and assigned High priority. Public threat-intelligence validation returned no result because the IP was private, but the alert remained relevant due to known lab attacker context. The event was retained for escalation and follow-up handling. :contentReference[oaicite:4]{index=4}

## 14. Practical Interpretation
This activity demonstrates that effective alert triage depends not only on public threat-intelligence enrichment, but also on analyst context. In this case, the lack of public intelligence did not reduce the relevance of the alert because the source IP was already known as part of lab attacker activity. This is an important SOC principle: internal context can be as important as external reputation data during triage.
