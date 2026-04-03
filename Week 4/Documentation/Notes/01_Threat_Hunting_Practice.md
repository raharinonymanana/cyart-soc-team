# Week 4 – Threat Hunting Practice

## 1. Objective
The objective of this activity was to perform a hypothesis-driven threat hunting exercise on the monitored Windows endpoint. The task required the use of Wazuh / Elastic Security, Velociraptor, and threat-intelligence validation to develop a hunting hypothesis, query logs, validate findings, and summarize the result with a MITRE ATT&CK mapping.

## 2. Tools Used
- Wazuh / Elastic Discover
- Velociraptor
- Windows Event Viewer
- Windows PowerShell (Run as Administrator)
- AlienVault OTX / public threat-intelligence lookup logic

## 3. Hunting Hypothesis
A privileged administrative logon occurred on the monitored Windows host, and this activity should be visible in Windows Security logs as a privileged logon event and supported by endpoint evidence collected through Velociraptor.

## 4. Lab Context
The monitored endpoint used for this activity was the Windows host enrolled in Wazuh and Velociraptor.

### Endpoint Context
- Agent name: `WINDOWS-HOST-HASINA`
- Hostname: `Stifler`
- User involved: `Hasina Rindra`

## 5. Activity Performed
A privileged administrative action was generated on the Windows endpoint by opening PowerShell with elevated privileges. The event was first confirmed locally in Event Viewer and then hunted in Wazuh Discover using Windows Security event filters. After identifying the privileged event, Velociraptor was used to collect live process information from the same host in order to support endpoint-side validation.

## 6. Wazuh Log Query and Confirmed Finding
The following event was identified in Wazuh Discover:

| Timestamp | User | Event ID | Host | Notes |
|---|---|---:|---|---|
| Mar 31, 2026 @ 23:00:08.974 | Hasina Rindra | 4672 | Stifler | Special privileges assigned to new logon |

### Event Details
- Agent name: `WINDOWS-HOST-HASINA`
- Agent ID: `001`
- Agent IP: `192.168.56.1`
- Event channel: `Security`
- Provider: `Microsoft-Windows-Security-Auditing`
- Severity: `AUDIT_SUCCESS`
- Rule ID: `67028`
- Rule level: `3`
- Rule description: `Special privileges assigned to new logon.`

### Privileges Observed
- SeSecurityPrivilege
- SeTakeOwnershipPrivilege
- SeLoadDriverPrivilege
- SeBackupPrivilege
- SeRestorePrivilege
- SeDebugPrivilege
- SeSystemEnvironmentPrivilege
- SeImpersonatePrivilege
- SeDelegateSessionUserImpersonatePrivilege

### Interpreted Meaning
This event confirms that the account **Hasina Rindra** received special privileges at logon on the monitored Windows host. This is consistent with a privileged or elevated administrative session and supports the hunting hypothesis focused on suspicious privileged account activity.

## 7. Velociraptor Validation
Velociraptor was successfully connected to the same Windows host and the artifact `Windows.System.Pslist` was executed. The process collection returned live process data from the endpoint. The results showed core Windows processes including:

- `smss.exe`
- `csrss.exe`
- `wininit.exe`
- `winlogon.exe`
- `services.exe`

The `winlogon.exe` process was shown as elevated and running under `NT AUTHORITY\SYSTEM`, which supports the presence of a privileged Windows session on the same host.

### Velociraptor Interpretation
The Velociraptor result confirms that:
- the Windows endpoint was reachable and actively collectible,
- live process data was successfully acquired,
- privileged Windows session context was present on the endpoint.

However, the Pslist output did not provide a direct one-to-one correlation to the exact PowerShell process or the exact user session responsible for Event ID 4672. Therefore, the endpoint-side correlation is supportive rather than definitive.

## 8. Threat Intelligence Validation
Threat-intelligence validation was planned using public lookup logic for the observed host IP. Because the endpoint IP belonged to a private/internal lab range, no meaningful public intelligence result was expected. In this case, the lack of public intelligence did not weaken the hunt because the host and account context were already known from the monitored lab environment.

## 9. Correlation Analysis
The Wazuh finding provides the strongest evidence in this activity. Event ID 4672 clearly records that special privileges were assigned to a new logon for user **Hasina Rindra** on host **Stifler**. Velociraptor process collection on the same endpoint returned elevated Windows session processes, especially `winlogon.exe`, which supports the existence of a privileged host state. Although the process results did not show a direct `powershell.exe` row tied to the event, the same host, same activity window, and same privileged session context are sufficient for a valid hunting conclusion in this lab.

## 10. MITRE ATT&CK Mapping
### Primary Hunting Interpretation
- **T1078 – Valid Accounts**

### Wazuh Rule-Side Mapping Observed
- **T1484 – Domain Policy Modification**

### Analyst Note
For this threat hunting exercise, the event was interpreted primarily under **T1078** because the activity reflected privileged account use on the monitored endpoint, which matches the objective of hunting for suspicious account or privilege use.

## 11. 100-Word Hunting Report
A hypothesis-driven threat hunting exercise was conducted on the monitored Windows endpoint to identify suspicious privileged account activity. Wazuh Discover confirmed Event ID 4672 on host Stifler for user Hasina Rindra, showing that special privileges were assigned to a new logon. This indicated a privileged administrative session and provided the main evidence for the hunt. Velociraptor process collection on the same endpoint returned live host data, including elevated Windows session processes such as winlogon.exe, which supported the Wazuh finding. Although the exact PowerShell process was not directly captured in Pslist, the event correlation was sufficient to map the activity to MITRE ATT&CK T1078.





