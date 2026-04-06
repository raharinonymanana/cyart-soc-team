# Week 1 — Foundations of Security Operations and Threat Monitoring

**Intern:** Hasina Rindra  
**Field:** SOC Intern @ CyArt Tech LLP  
**Date:** 05/03/2026  
**Repository:** [cyart-soc-team](https://github.com/raharinonymanana/cyart-soc-team)

---

## Objective

Build foundational knowledge of Security Operations Center (SOC) workflows — from theory to hands-on practice — covering security monitoring, log analysis, detection logic, and incident response basics.

## What Was Covered

### Theoretical Foundations
- SOC structure, roles (Tier 1/2/3, SOC Manager, Threat Hunter), and operational workflow (Detection → Triage → Investigation → Response)
- Core security concepts: CIA Triad, Threat vs Vulnerability vs Risk, Defense-in-Depth, Zero Trust
- Log management lifecycle: Collection → Normalization → Storage → Retention → Analysis
- Incident response stages (NIST SP 800-61): Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned
- MITRE ATT&CK framework for mapping adversary tactics and techniques
- Documentation standards: runbooks, SOPs, incident reports, post-incident reviews

### Practical Lab Work

**Lab Setup:**
- **Laptop 1 (VirtualBox):** Kali Linux (attack simulation), Ubuntu (ELK stack host), Metasploitable (vulnerable target)
- **Laptop 2:** Kibana dashboard access + research

**Activities performed:**

| Activity | Tools Used | Key Takeaway |
|---|---|---|
| Windows Event Log analysis | Event Viewer | Reviewed Event IDs 4625, 4624, 4672, 4688, 4798, 4720, 1102 for security-relevant activity |
| Failed login detection (Event ID 4625) | Event Viewer | Identified brute-force patterns via repeated authentication failures → maps to **MITRE ATT&CK T1110 (Brute Force)** |
| New service creation detection (Event ID 7045) | Event Viewer | Flagged unauthorized service installs → maps to **MITRE ATT&CK T1543.003 (Create or Modify System Process: Windows Service)** |
| Pattern recognition across events | Event Viewer | Correlated failed logins + service creation to identify multi-stage attack indicators |
| Browser history forensics | Forensic URL review tools | Extracted browsing artifacts to support investigation timelines (phishing, C2 access) |
| Monitoring dashboard setup | Kibana (ELK) | Built dashboards for top alert sources, event frequency trends, and critical event visualization |
| Threshold-based alert rules | Kibana Observability Alerts | Created detection rule for repeated failed logins within a time window, tested with simulated events |
| IDS rule testing | Snort on Kali | Wrote ICMP detection rule, validated with ping from Windows machine → maps to **MITRE ATT&CK T1018 (Remote System Discovery)** |
| Vulnerability scanning | Nessus | Scanned Metasploitable to identify missing patches, insecure services, known CVEs |
| Endpoint visibility | Osquery | Queried running processes, open ports, installed software on endpoints for investigation support |
| Event documentation | Structured template | Recorded date, source IP, event ID, description, and analyst action for each finding |

## Key Event IDs Reference

| Event ID | Description | SOC Relevance |
|---|---|---|
| 4625 | Failed logon attempt | Brute-force detection, credential stuffing |
| 4624 | Successful logon | Baseline auth tracking, lateral movement |
| 4672 | Special privileges assigned | Privilege escalation monitoring |
| 4688 | New process created | Suspicious execution (PowerShell, cmd) |
| 4798 | User's local group membership enumerated | Discovery / reconnaissance |
| 4720 | User account created | Persistence, unauthorized account creation |
| 1102 | Audit log cleared | Anti-forensics / defense evasion |
| 7045 | New service installed | Persistence mechanism |

## MITRE ATT&CK Mapping

| Observed Activity | Tactic | Technique |
|---|---|---|
| Repeated failed logins | Credential Access | T1110 — Brute Force |
| New service creation | Persistence | T1543.003 — Windows Service |
| PowerShell execution | Execution | T1059.001 — PowerShell |
| Log clearing | Defense Evasion | T1070.001 — Clear Windows Event Logs |
| Group enumeration | Discovery | T1069 — Permission Groups Discovery |
| ICMP ping sweep | Discovery | T1018 — Remote System Discovery |
| Account creation | Persistence | T1136.001 — Local Account |

## Tools Used

- **SIEM:** ELK Stack (Elasticsearch, Logstash, Kibana) — deployed on Ubuntu VM
- **IDS:** Snort — rule-based network traffic detection on Kali
- **Vulnerability Scanner:** Nessus Essentials — asset exposure assessment
- **Endpoint Monitoring:** Osquery — host-level process/service inspection
- **Attack Simulation:** Kali Linux + Metasploitable (controlled lab)
- **Log Source:** Windows Event Viewer (Security, System, Application logs)

## Challenges & Lessons Learned

- **Alert tuning is critical** — overly sensitive thresholds create noise; too restrictive misses real threats. Finding the right balance is a core SOC skill.
- **Single events rarely confirm malicious activity** — pattern recognition and correlation across sources (host logs + network + endpoint state) is what separates noise from incidents.
- **Manual log review doesn't scale** — essential for learning log structure, but SIEM dashboards and automated alerting are necessary for operational SOC work.
- **Documentation is not optional** — structured evidence recording enables escalation, post-incident review, and cross-analyst communication.

## Repository Structure

```
cyart-soc-team/
├── Week-1/          ← You are here
│   ├── README.md
│   └── Week-1_Report.pdf
├── Week-2/
├── Week-3/
└── Week-4/
```

## References

- NIST SP 800-61 — Computer Security Incident Handling Guide
- NIST Cybersecurity Framework (CSF)
- MITRE ATT&CK Framework — https://attack.mitre.org
- Elastic / Kibana Documentation — https://www.elastic.co/guide
- Wazuh Documentation — https://documentation.wazuh.com
- Snort Documentation — https://www.snort.org/documents
- Nessus Essentials — https://www.tenable.com/products/nessus
- Osquery Documentation — https://osquery.readthedocs.io
