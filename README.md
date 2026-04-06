# CyArt SOC Team — Internship Task Submissions

**Intern:** Hasina Rindra  
**Program:** MScIT, Cybersecurity & Forensics  
**Organization:** CyArt Tech LLP — SOC Department  
**Period:** March–April 2026

---

## About This Repository

This repository documents my weekly SOC internship tasks at CyArt Tech, progressing from foundational security operations concepts to hands-on incident handling and threat intelligence workflows. Each week builds on the previous one.

## Weekly Submissions

| Week | Title | Focus Areas | Link |
|---|---|---|---|
| 1 | Foundations of Security Operations and Threat Monitoring | SOC fundamentals, log analysis (Windows Event IDs), Kibana dashboards, threshold-based alert rules, Snort IDS, Nessus scanning, Osquery, MITRE ATT&CK mapping | [Week 1](./Week%201) |
| 2 | SOC Documentation and Alert Management | Alert triage, response documentation, evidence preservation, SOC workflow documentation, capstone task | [Week 2](./Week%202) |
| 3 | Advanced Log Analysis and Threat Intelligence | Advanced log analysis, threat intelligence integration, incident escalation workflows, alert triage with TI, evidence preservation, full SOC workflow simulation | [Week 3](./Week%203) |
| 4 | *(See folder for details)* | Documentation, advanced workflows | [Week 4](./Week%204) |

## SOC Environment

- **SIEM:** Wazuh + ELK Stack (Elasticsearch, Logstash, Kibana)
- **EDR:** Velociraptor
- **IDS:** Snort
- **Vulnerability Scanner:** Nessus Essentials
- **Endpoint Monitoring:** Osquery
- **Log Sources:** Windows Event Logs, Firewall logs, Syslog
- **Lab Setup:** VirtualBox (Kali Linux, Ubuntu, Metasploitable) + Windows host for Kibana access

## Frameworks & Standards Referenced

- **NIST SP 800-61** — Incident Response lifecycle
- **MITRE ATT&CK** — Adversary tactic/technique mapping
- **NIST CSF** — Cybersecurity Framework
- **CIA Triad / Defense-in-Depth / Zero Trust** — Core security principles

## Folder Structure

```
cyart-soc-team/
├── README.md              ← You are here
├── Week 1/
│   ├── Workflow/
|--- |---- README.md          
│   └── Week-1_Hasina-Rindra.pdf
├── Week 2/
│   └── ...
├── Week 3/
│   └── ...
└── Week 4/
    ├── Documentation/
    ├── Workflow/
    └── README.md
```

## What Each Weekly Folder Contains

- **README.md** — Summary of objectives, activities, tools used, and key findings
- **PDF report** — Full structured internship task document
- **Workflow/** — Screenshots, configurations, and step-by-step evidence
- **Documentation/** — Supporting notes and reference materials (where applicable)

## Purpose

This repository serves as both a learning record and professional portfolio of SOC analyst skills developed during the internship, covering monitoring, detection, triage, investigation, documentation, and incident response.
