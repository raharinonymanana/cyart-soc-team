# Week 4 – Evidence Analysis

## 1. Objective
The objective of this activity was to analyze collected host evidence, identify relevant network-state findings, preserve the evidence in a saved file, and document a chain-of-custody record. The Week 4 task specifically requires evidence analysis using Velociraptor, identification of suspicious or relevant network connections, and maintenance of a chain-of-custody entry with item, description, collector, date, and hash value. :contentReference[oaicite:0]{index=0}

## 2. Tools Used
- Velociraptor
- Windows Command Prompt / PowerShell
- SHA256 hashing utility (`certutil` or `Get-FileHash`)
- Manual evidence documentation

## 3. Lab Adaptation
The original task mentions Velociraptor and FTK Imager. In this lab instance, Velociraptor was fully operational on the monitored Windows endpoint, while FTK Imager was not required for the minimum evidence workflow. Evidence preservation was therefore performed through:
1. Velociraptor artifact collection,
2. local evidence-file saving,
3. SHA256 hashing,
4. chain-of-custody documentation. :contentReference[oaicite:1]{index=1}

## 4. Evidence Source Selected
The evidence source selected for this activity was the Velociraptor artifact:

`Windows.Network.Netstat`

The evidence was collected from the monitored Windows endpoint:

- Host: `Stifler`
- Agent: `WINDOWS-HOST-HASINA`

The Netstat artifact was chosen because the Week 4 task explicitly asks for network-connection evidence similar to `SELECT * FROM netstat`. :contentReference[oaicite:2]{index=2}

## 5. Evidence Collection Summary
The Velociraptor Netstat collection successfully returned live network-state information from the monitored Windows host. The result set contained listening and established network entries that reflected the current communication state of the endpoint at the time of collection.

The most relevant rows observed during analysis included:
- `sshd.exe` listening on `0.0.0.0:22`
- `svchost.exe` listening on `0.0.0.0:135`
- `System` listening on `192.168.56.1:139`
- additional local and established connections associated with standard host services

## 6. Key Evidence Finding
The strongest evidence item identified in the collection was:

- **Process:** `sshd.exe`
- **State:** `LISTEN`
- **Local Address:** `0.0.0.0`
- **Local Port:** `22`

### Interpretation
This finding indicates that the SSH service was listening on all interfaces of the monitored Windows host at the time of collection. In a security analysis context, this is the most important host-based evidence row because it shows that a remote-access service was exposed and available on the endpoint.

## 7. Supporting Evidence Interpretation
Additional connection rows appeared to reflect normal or expected lab-management traffic, including endpoint-monitoring and local service activity. These rows were useful because they demonstrated that:
- the host was actively communicating,
- the monitoring tooling was functioning,
- the evidence came from a live and responsive endpoint.

The evidence therefore served two purposes:
1. it preserved the host’s network state,
2. it provided a basis for distinguishing important listening services from normal management traffic.

## 8. Evidence Preservation
The evidence output was preserved by saving the relevant Netstat collection into a local evidence file.

### Evidence File
- `Week4_Netstat_Evidence.txt`  
or  
- `Week4_Netstat_Evidence.csv`

A SHA256 hash was then generated to preserve integrity and support verification.

## 9. Hashing Method
A SHA256 hash was generated for the saved evidence file after collection. This ensured that the evidence could later be verified for integrity and demonstrated proper handling of digital forensic material.

### Hashing Logic Used
- Evidence file created and stored locally
- SHA256 generated using a system hashing utility
- Hash value recorded in the chain-of-custody table
- Hash output preserved as supporting proof

## 10. Chain-of-Custody Record

| Item | Description | Collected By | Date | Hash Value |
|---|---|---|---|---|
| Network Connection Evidence | Velociraptor `Windows.Network.Netstat` result from monitored Windows host Stifler, showing `sshd.exe` listening on `0.0.0.0:22` and additional listening/established connections | Hasina (SOC Analyst) | 2026-04-01 | [PASTE YOUR SHA256 HERE] |

## 11. Analyst Evidence Note
The following analyst note summarizes the evidence handling:

> The evidence output was preserved by manually saving the Velociraptor `Windows.Network.Netstat` result into a local evidence file and generating a SHA256 hash for integrity verification. The most relevant finding was `sshd.exe` listening on `0.0.0.0:22`, indicating that the SSH service was exposed on all interfaces of the monitored Windows endpoint at the time of collection.

## 12. Evidence Relevance
This evidence is relevant because it captures the live network-state condition of the monitored endpoint. Unlike a general screenshot alone, the preserved evidence file and its hash create a traceable and reproducible record of the host’s listening and established connections. This makes the evidence suitable for both incident review and formal documentation.

## 13. Relationship to Other Week 4 Activities
This evidence analysis supports the broader Week 4 workflow in several ways:
- it strengthens host-side validation begun in Threat Hunting Practice,
- it supports triage and incident review with concrete endpoint evidence,
- it demonstrates evidence preservation practices required for SOC and forensic work.

## 14. Practical Interpretation
The collection did not by itself prove malicious activity. However, it successfully demonstrated proper evidence handling and identified a meaningful security-relevant service exposure (`sshd.exe` on port 22). In a real investigation, this would justify further validation of service purpose, access controls, exposure scope, and surrounding authentication activity.
