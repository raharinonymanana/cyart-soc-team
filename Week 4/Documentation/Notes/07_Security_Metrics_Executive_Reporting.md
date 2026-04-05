# Task 7 — Security Metrics and Executive Reporting

## Overview

**Objective:** Calculate advanced SOC performance metrics and produce an executive-level report communicating SOC effectiveness.  
**Tools Used:** Elastic Security, Google Sheets, Google Docs  
**Date:** 2025-04-03  
**Analyst:** SOC Intern — CyArt Tech India  

---

## 1. SOC Metrics — Definitions

| Metric                  | Definition                                                                 |
|-------------------------|----------------------------------------------------------------------------|
| MTTD (Mean Time to Detect) | Average time from attacker initial access to SOC detection              |
| MTTR (Mean Time to Respond) | Average time from detection to containment/resolution                  |
| Dwell Time              | Total time attacker remains undetected in the environment                  |
| False Positive Rate     | Percentage of alerts that are non-malicious out of total alerts generated  |
| Incident Resolution Rate | Percentage of incidents fully resolved within SLA window                  |

---

## 2. Metrics Dashboard — Mock Incident Data

### Incident Reference: INC-2025-0403

| Metric                  | Value         | Notes                                              |
|-------------------------|---------------|----------------------------------------------------|
| Attack Start Time       | 07:00:00      | Estimated from firewall log first anomaly           |
| First Alert Generated   | 09:00:00      | Wazuh Rule 92200 triggered                         |
| MTTD                    | **2 hours**   | 09:00 - 07:00                                      |
| Containment Completed   | 13:00:00      | IP blocked via CrowdSec, VM isolated               |
| MTTR                    | **4 hours**   | 13:00 - 09:00                                      |
| Dwell Time              | **6 hours**   | 13:00 - 07:00 (full attacker presence window)      |
| Total Alerts (Week)     | 87            | All sources combined                               |
| False Positives         | 7             | Verified benign after investigation                |
| False Positive Rate     | **8.04%**     | (7 / 87) × 100                                    |
| Incidents Resolved      | 5 of 5        | All within 24-hour SLA window                     |
| Incident Resolution Rate | **100%**     | Full week                                          |

---

## 3. Metrics Calculation Detail

### MTTD
```
Attack Start:     07:00:00
First Detection:  09:00:00
MTTD = 09:00 - 07:00 = 2 hours
```

### MTTR
```
Detection Time:      09:00:00
Containment Time:    13:00:00
MTTR = 13:00 - 09:00 = 4 hours
```

### Dwell Time
```
Attack Start:        07:00:00
Full Containment:    13:00:00
Dwell Time = 13:00 - 07:00 = 6 hours
```

### False Positive Rate
```
Total Alerts:        87
False Positives:     7
FPR = (7 / 87) × 100 = 8.04%
```

---

## 4. Dwell Time Analysis (50 Words)

For incident INC-2025-0403, the attacker maintained undetected presence for 6 hours between initial access (07:00) and full containment (13:00). The 2-hour detection delay indicates a gap in real-time endpoint monitoring. Reducing dwell time requires tuning Wazuh rules for earlier behavioural anomaly detection and enabling continuous network traffic analysis.

---

## 5. Elastic Security Dashboard — Configuration Notes

**Dashboard Name:** `SOC-Performance-Week4`  
**Index Pattern:** `wazuh-alerts-*`

| Panel | Visualization Type | Query / Field |
|-------|--------------------|---------------|
| MTTD Over Time | Line Chart | `rule.level >= 10` grouped by hour |
| MTTR Per Incident | Bar Chart | `incident.resolution_time` |
| False Positive Rate | Gauge | `alert.verdict: false_positive` |
| Dwell Time | Metric | Custom calculated field |
| Alert Volume | Area Chart | `@timestamp` per day |

---

## 6. Executive Summary (150 Words)

**To:** CyArt Tech India — Security Leadership  
**From:** SOC Analyst Team  
**Re:** SOC Performance Report — Week of April 3, 2025  

During the reporting period, the SOC successfully detected and contained one security incident (INC-2025-0403) involving simulated spearphishing activity mapped to MITRE ATT&CK T1566. Key performance metrics recorded a Mean Time to Detect (MTTD) of 2 hours and a Mean Time to Respond (MTTR) of 4 hours, with an overall attacker dwell time of 6 hours. The false positive rate stood at 8.04%, within acceptable operational thresholds. All five open incidents were resolved within the 24-hour SLA window, achieving a 100% incident resolution rate for the week.

**Recommendations:** Implement additional Wazuh detection rules targeting user execution behaviours (T1204) to reduce MTTD below 1 hour. Integrate email gateway telemetry to close identified coverage gaps and further reduce dwell time in future incidents.

---

## 7. References

- NIST SP 800-61 Rev 2: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final
- CISA Cybersecurity Metrics: https://www.cisa.gov/
- Elastic Security Dashboards: https://www.elastic.co/guide/en/security/current/dashboards-overview.html
