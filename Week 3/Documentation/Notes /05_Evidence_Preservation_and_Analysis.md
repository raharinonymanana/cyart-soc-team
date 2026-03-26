# 05_Evidence_Preservation_and_Analysis

## Overview
Evidence preservation and analysis are essential in incident response because important system data can be lost, altered, or overwritten if it is not collected properly. In this task, volatile network information and memory evidence were collected from a Windows virtual machine, hashed for integrity, and documented in a structured evidence record.

## Objective
The objective of this exercise was to:
- collect volatile evidence from a live system;
- acquire a memory dump from the Windows VM;
- generate a SHA-256 hash to verify evidence integrity;
- document the evidence in a chain-of-custody style format;
- support later investigation with preserved forensic artifacts.

## Tools Used
- Velociraptor
- FTK Imager
- sha256sum
- Windows VM / Server-Y lab host
- Google Sheets / analyst notes

## Evidence Collection Scenario
A Windows virtual machine associated with Server-Y was treated as a system of interest after suspicious activity was identified. The priority was to preserve volatile data before the system state changed.

The evidence collection sequence followed this order:
1. active network connections;
2. memory acquisition;
3. hash generation;
4. evidence documentation.

This order was chosen because volatile evidence can be lost if the host is shut down or modified.

## Volatile Data Collection
Velociraptor was used to collect active network connection data from the Windows VM.

**Query used:**  
`SELECT * FROM netstat`

The output was exported for later review and preservation.

### Sample Network Connection Record
| Timestamp | Protocol | Local Address | Remote Address | State | Observation |
|---|---|---|---|---|---|
| 2025-08-18 13:32:10 | TCP | 192.168.1.50:49720 | 192.168.1.101:445 | ESTABLISHED | Internal SMB-related connection |
| 2025-08-18 13:32:14 | TCP | 192.168.1.50:49802 | 198.51.100.23:443 | ESTABLISHED | Suspicious outbound encrypted connection |
| 2025-08-18 13:32:16 | UDP | 192.168.1.50:5353 | 224.0.0.251:5353 | LISTENING | Standard multicast service |
| 2025-08-18 13:32:19 | TCP | 192.168.1.50:49815 | 203.0.113.20:80 | TIME_WAIT | Recent outbound web communication |

This data provided a live snapshot of active communication at the time of collection.

## Memory Acquisition
After collecting network connection data, a memory dump was acquired using Velociraptor.

**Query used:**  
`SELECT * FROM Artifact.Windows.Memory.Acquisition`

The memory dump was saved as an evidence item for further analysis.

**Evidence Item:** Server-Y Dump

Memory acquisition is valuable because it can contain active processes, injected code, suspicious scripts, credentials in memory, and other evidence not always visible in normal log files.

## Hashing for Integrity
Once the memory dump was collected, a SHA-256 hash was generated to verify file integrity.

**Command used:**  
`sha256sum server-y-memory-dump.raw`

### Example Hash Output
`8f7d3c85f2b9d6e4b247f6c0e8f9b6c4f31aab4d1c4c9a2e6f5e981d7a3b4c10  server-y-memory-dump.raw`

This hash value was recorded to ensure the evidence can be verified later and confirmed as unchanged.

## Evidence Documentation
The memory dump and collected data were documented in a structured evidence record.

### Evidence Log
| Item | Description | Collected By | Date | Hash Value |
|---|---|---|---|---|
| Memory Dump | Server-Y Dump | SOC Analyst | 2025-08-18 | 8f7d3c85f2b9d6e4b247f6c0e8f9b6c4f31aab4d1c4c9a2e6f5e981d7a3b4c10 |

### Extended Evidence Record
| Evidence ID | Item Type | Source System | Collected By | Date and Time | Acquisition Method | Storage Location | Integrity Status |
|---|---|---|---|---|---|---|---|
| EV-005 | Memory Dump | Server-Y | SOC Analyst | 2025-08-18 13:40:00 | Velociraptor Artifact.Windows.Memory.Acquisition | Secured Evidence Folder | SHA-256 Verified |
| EV-006 | Netstat CSV | Server-Y | SOC Analyst | 2025-08-18 13:35:00 | Velociraptor SELECT * FROM netstat | Secured Evidence Folder | Export Preserved |

This documentation helps maintain traceability and supports later forensic review.

## Findings
The task showed that volatile evidence such as active connections should be collected before major response actions change the system state. Memory acquisition preserved a more detailed snapshot of the host, while hashing ensured that the main evidence item could be validated later. Structured evidence logging improved traceability and supported proper investigative handling.

## SOC Value
This exercise demonstrated the importance of forensic discipline in SOC operations. Preserving evidence correctly allows analysts to investigate incidents more effectively, validate attacker behavior, and provide reliable artifacts for escalation or forensic review.

## Conclusion
This exercise successfully demonstrated evidence preservation and analysis using Velociraptor, memory acquisition, SHA-256 hashing, and structured evidence documentation. The task reflected good incident response practice by prioritizing volatile data, protecting integrity, and maintaining a clear record of collected artifacts.

## Screenshot Reference
**Suggested screenshot for this note:** `10_Velociraptor_Collection.png`  
Optional additional screenshots:
- `11_Memory_Dump_Hash.png`
