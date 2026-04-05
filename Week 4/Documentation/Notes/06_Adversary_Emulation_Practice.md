Objective
Simulate adversary TTPs using MITRE Caldera against a Windows 11 endpoint and validate SOC detection capabilities with Wazuh SIEM.
Tools Used

MITRE Caldera 5.3.0 — Adversary emulation platform (installed on Kali Linux)
Wazuh 4.x — SIEM for alert detection and MITRE ATT&CK mapping
Sandcat Agent — Caldera's default agent deployed on Windows 11 target

Lab Environment
MachineRoleIPKali Linux VMCaldera C2 Server192.168.56.102Windows 11 HostTarget (Wazuh Agent)192.168.56.1Ubuntu VMWazuh Server192.168.56.105
Activity Performed
Step 1 — Caldera Server Setup
Installed MITRE Caldera 5.3.0 on Kali Linux at /opt/caldera. Built the Magma Vue UI plugin manually using npm install && npm run build. Server launched on http://0.0.0.0:8888 with default credentials.
Step 2 — Agent Deployment
Deployed the Sandcat agent on Windows 11 via PowerShell. The agent connected back to the Caldera server over HTTP, registering as host Stifler with Elevated privileges in the red group.
Agent deployment command executed from an Administrator PowerShell session, downloading and executing splunkd.exe (Caldera's disguised agent binary).
Step 3 — Adversary Operation Execution
Created and launched an operation using a discovery-focused adversary profile. Caldera autonomously executed the following TTPs on the Windows 11 target:
TimestampTTPAbility NameTacticDetection StatusNotes2026-04-05 17:12:00 ISTT1033Identify active userDiscoveryDetectedWazuh logged process execution2026-04-05 17:12:15 ISTT1082System informationDiscoveryDetectedsysteminfo command captured2026-04-05 17:12:30 ISTT1016Network configurationDiscoveryDetectedipconfig /all execution logged2026-04-05 17:12:45 ISTT1057Process discoveryDiscoveryDetectedtasklist command detected2026-04-05 17:13:00 ISTT1083File and directory scanDiscoveryDetecteddir command execution logged2026-04-05 17:13:15 ISTT1078Valid accounts checkPrivilege Esc.DetectedAccount enumeration detected
Step 4 — Wazuh Detection Analysis
After shutting down Kali and booting the Wazuh server (Ubuntu), the Wazuh agent on Windows forwarded buffered events. Wazuh detected 2,053 MITRE-mapped events including:

T1078 — Valid Accounts (Defense Evasion, Persistence, Privilege Escalation)
T1548.003 — Sudo and Sudo Caching (Privilege Escalation)
1,074 authentication_failed events from earlier brute force activity

Emulation Report (100 words)
The Caldera adversary emulation exercise validated SOC detection capabilities against automated TTP execution. Using a discovery-focused adversary profile, Caldera executed six TTPs (T1033, T1082, T1016, T1057, T1083, T1078) against a Windows 11 endpoint. Wazuh successfully detected all discovery-phase activities through Windows Event Log monitoring, generating 2,053 MITRE-mapped alerts. Key detection gap identified: initial agent deployment (Sandcat) was not flagged by Wazuh — only McAfee AV blocked an earlier payload attempt. Recommendation: implement Wazuh rules for suspicious process creation from C:\Users\Public\ and enhance monitoring of PowerShell download cradles (Invoke-WebRequest) to close this gap.
MITRE ATT&CK Mapping
TechniqueNameTacticDetectedT1033System Owner/User DiscoveryDiscoveryYesT1082System Information DiscoveryDiscoveryYesT1016System Network Config DiscoveryDiscoveryYesT1057Process DiscoveryDiscoveryYesT1083File and Directory DiscoveryDiscoveryYesT1078Valid AccountsPrivilege EscalationYesT1059.001PowerShellExecutionPartial
Detection Gaps Identified

Agent deployment not detected by SIEM — Caldera's Sandcat agent was deployed and executed without triggering Wazuh alerts. Only McAfee AV caught the earlier Meterpreter payload.
PowerShell download cradle — Invoke-WebRequest used to download malicious payloads was not flagged by Wazuh default rules.
C2 communication — HTTP beaconing from the Sandcat agent to 192.168.56.102:8888 was not detected.

Recommendations

Add custom Wazuh rules to monitor process creation from C:\Users\Public\
Enable Sysmon integration with Wazuh for enhanced process monitoring
Implement PowerShell script block logging (Event ID 4104) and forward to Wazuh
Create Wazuh rules for HTTP beaconing pattern detection

Screenshots

Caldera dashboard with agent connected
Caldera operation running with TTPs
Wazuh MITRE ATT&CK mapped alerts (2,053 events)
