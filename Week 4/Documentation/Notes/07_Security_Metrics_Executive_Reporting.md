# 07 — Security Metrics and Executive Reporting

## Objective
Calculate advanced SOC metrics (MTTD, MTTR, dwell time, false positive rate) from real incident data and create an executive report presenting findings and recommendations.

## Tools Used
- **Wazuh Dashboard** — SIEM metrics and alert analysis
- **Manual Calculation** — Derived from attack/detection timeline

## Incident Timeline (Source Data)

| Event                              | Timestamp               | Notes                          |
|------------------------------------|-------------------------|--------------------------------|
| Attack initiated (Nmap scan)       | 2026-04-05 15:33 IST    | First reconnaissance activity  |
| SMB brute force started            | 2026-04-05 15:38 IST    | T1110 — Brute Force            |
| SSH brute force started            | 2026-04-05 15:45 IST    | T1110.001 — Password Guessing  |
| Payload delivered (update.exe)     | 2026-04-05 16:04 IST    | T1105 — Ingress Tool Transfer  |
| Payload blocked by McAfee          | 2026-04-05 16:08 IST    | AV detection triggered         |
| Wazuh alerts reviewed              | 2026-04-05 16:22 IST    | 1,074 auth_failed events found |
| Caldera emulation executed         | 2026-04-05 17:12 IST    | T1033, T1082, T1016 executed   |
| Containment (iptables block)       | 2026-04-05 17:30 IST    | Attacker IP blocked            |
| Incident closed                    | 2026-04-05 17:45 IST    | Cleanup completed              |

## Metrics Calculation

### Mean Time to Detect (MTTD)
**Formula:** MTTD = Time of Detection − Time of Attack Start

- Attack Start: 15:33 IST
- First Detection (McAfee AV alert): 16:08 IST
- MTTD for AV: **35 minutes**
- First SIEM Detection (Wazuh alert review): 16:22 IST
- MTTD for SIEM: **49 minutes**

**Average MTTD: 42 minutes**

### Mean Time to Respond (MTTR)
**Formula:** MTTR = Time of Containment − Time of Detection

- Detection: 16:08 IST
- Containment (IP blocked): 17:30 IST
- **MTTR: 1 hour 22 minutes (82 minutes)**

### Dwell Time
**Formula:** Dwell Time = Time of Containment − Time of Initial Compromise

- Initial compromise (first attack activity): 15:33 IST
- Containment: 17:30 IST
- **Dwell Time: 1 hour 57 minutes (117 minutes)**

### False Positive Rate
**Formula:** FP Rate = False Positives / Total Alerts × 100

- Total alerts generated: 8,049 (22 medium + 8,027 low severity)
- Estimated false positives (routine system events): ~7,000
- True positive alerts (attack-related): ~1,049
- **False Positive Rate: ~87%**

Note: High FP rate is expected in a lab environment with default Wazuh rules. In production, tuning rules and whitelisting known-good activity would reduce this significantly.

### Metrics Summary Table

| Metric               | Value         | Industry Benchmark | Assessment  |
|----------------------|---------------|--------------------|-------------|
| MTTD                 | 42 minutes    | 24-48 hours        | Excellent   |
| MTTR                 | 82 minutes    | 4-8 hours          | Good        |
| Dwell Time           | 117 minutes   | 21 days (median)   | Excellent   |
| False Positive Rate  | 87%           | < 50%              | Needs Work  |
| Total Alerts         | 8,049         | —                  | —           |
| MITRE-Mapped Alerts  | 2,053         | —                  | —           |

## Executive Report (150 words)

### SOC Performance Summary — Adversary Emulation Exercise

On April 5, 2026, the SOC conducted a controlled adversary emulation exercise targeting the Windows 11 endpoint using Metasploit and MITRE Caldera. The exercise simulated brute force attacks (T1110), malware delivery (T1105), and automated TTP execution across the discovery kill chain phase.

**Key Findings:** Detection capabilities performed well with an MTTD of 42 minutes and MTTR of 82 minutes — both significantly below industry benchmarks. Wazuh generated 2,053 MITRE-mapped alerts, and McAfee AV successfully blocked the malicious payload.

**Areas for Improvement:** The false positive rate of 87% indicates a need for rule tuning and baseline establishment. Additionally, C2 agent deployment was not detected by the SIEM.

**Recommendations:**
1. Implement Sysmon integration for enhanced process monitoring
2. Tune Wazuh rules to reduce false positives by 50%
3. Add custom detection rules for PowerShell download cradles
4. Establish regular emulation exercises quarterly

## Dwell Time Analysis (50 words)

Dwell time for the simulated incident was 117 minutes — from initial reconnaissance at 15:33 IST to containment at 17:30 IST. This is significantly below the industry median of 21 days, demonstrating effective monitoring. However, automated containment (SOAR playbook integration) could reduce dwell time further to under 30 minutes.

## Screenshots
- Wazuh dashboard showing alert severity distribution (22 medium, 8,027 low)
- Wazuh Threat Hunting view with 2,053 MITRE-mapped events
- Metrics calculation summary
