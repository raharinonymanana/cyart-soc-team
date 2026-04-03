# Week 4 – SOAR Playbook Development

## 1. Objective
This activity focused on designing and testing a SOAR-style incident response playbook for automated alert handling. The Week 4 task requires a playbook that reviews an alert, validates the indicator, applies a blocking action, creates an incident record, and summarizes the workflow result. :contentReference[oaicite:1]{index=1}

## 2. Tools Used
- Wazuh
- TheHive
- CrowdSec
- Manual documentation

## 3. Lab Adaptation
The original task references Splunk Phantom as the SOAR platform. In this lab, Splunk Phantom was not deployed, so the playbook was adapted to the available tools: Wazuh for detection, TheHive for case handling, and CrowdSec for containment. This kept the workflow aligned with the task objective while remaining realistic for the lab environment. :contentReference[oaicite:2]{index=2}

## 4. Trigger Alert Selected
For this playbook, a previously observed Week 3 Wazuh alert was reused as the trigger event. The selected alert was an authentication-related alert associated with failed access activity and a Kali source IP used in the lab.

### Alert Context
- Alert category: authentication failure / failed SSH-related activity
- Source IP: `192.168.56.102`
- Source context: Kali lab machine
- Relevant Wazuh rule context: authentication failure and access-control-related event
- Example reviewed timestamp: `Mar 31, 2026 @ 23:00:21.969`

This alert was suitable for SOAR testing because it provided a real lab indicator that could be validated, escalated into a case, and used for a containment decision.

## 5. Purpose of the Playbook
The purpose of the playbook was to standardize the first-response workflow for repeated suspicious authentication alerts. Instead of responding manually from the beginning each time, the workflow defines a repeatable sequence:

1. review the alert in Wazuh,
2. validate the source indicator,
3. create a case in TheHive,
4. block the source IP through CrowdSec,
5. document the outcome.

This mirrors the task example of checking IP reputation, blocking an IP, and creating a TheHive ticket. :contentReference[oaicite:3]{index=3}

## 6. Playbook Workflow Designed
The adapted playbook followed this logic:

### Step 1 – Alert review in Wazuh
The analyst reviews the selected authentication-failure alert and extracts the main fields:
- timestamp,
- alert description,
- source IP,
- target host,
- severity or rule level.

### Step 2 – Indicator validation
The suspicious source IP is validated using available context. In this case, the source IP belonged to the Kali attacker machine used in the controlled lab, which made it a valid test indicator for the playbook.

### Step 3 – Incident case creation
A case is created in TheHive with the alert summary, source IP, host context, and analyst note.

### Step 4 – Containment action
The suspicious source IP is blocked in CrowdSec as the containment step.

### Step 5 – Verification and documentation
The analyst verifies that the block decision exists and records the outcome of the workflow.

## 7. Tailored Playbook Scenario
The playbook was tailored around the following lab replay scenario:

A suspicious failed authentication alert associated with Kali source IP `192.168.56.102` is reviewed in Wazuh. Because the source is linked to attacker activity from the lab, the analyst treats the event as a valid high-priority trigger for containment testing. The indicator is escalated into a TheHive case and the source IP is marked for blocking in CrowdSec as part of the SOAR workflow test.

## 8. Test Execution Status
The selected alert was confirmed and used as the trigger condition for the playbook design. The workflow was therefore tailored to a real lab finding rather than a generic example. Because the conversation did not capture a final TheHive case ID or CrowdSec decision output, the playbook is documented as a lab-tested workflow design based on a validated alert, with case creation and blocking represented as intended response steps rather than fully evidenced final outputs.

## 9. Playbook Test Table

| Playbook Step | Status | Notes |
|---|---|---|
| Check alert in Wazuh | Success | Previous authentication-related alert reviewed and selected as trigger |
| Validate source IP | Success | Kali source IP `192.168.56.102` identified as relevant lab indicator |
| Assign priority | Success | Treated as High for playbook containment testing |
| Create case in TheHive | Simulated / Planned | Case logic defined for escalation, title and details prepared |
| Block IP via CrowdSec | Simulated / Planned | Source IP selected as the containment target |
| Document workflow | Success | Workflow adapted to real Week 3 alert and recorded for Week 4 submission |

## 10. Example TheHive Case Content
The following case structure was prepared for the playbook:

**Title:**  
`[High] Failed Authentication Alert from Kali Source IP`

**Severity:**  
High

**Description:**  
A previously observed authentication-related Wazuh alert from the lab environment was selected as the trigger event for the Week 4 SOAR playbook activity. The event involved suspicious failed access behavior associated with Kali source IP `192.168.56.102`. The alert was validated in the context of prior lab attacker activity and escalated for case tracking and containment testing.

**Core Observables:**  
- Source IP: `192.168.56.102`
- Alert type: Authentication failure / failed SSH-related activity
- Alert source: Wazuh
- Environment: Week 3 lab replay used for Week 4 SOAR workflow development

## 11. Example CrowdSec Containment Logic
The containment action prepared for this playbook was to block the identified source IP in CrowdSec for a temporary duration as part of a lab response test. The target indicator for the decision was:

`192.168.56.102`

This matched the containment stage expected by the task’s sample playbook flow. :contentReference[oaicite:4]{index=4}

## 12. Practical Interpretation
This activity demonstrates how an alert from Wazuh can be moved through a structured SOAR-style response path even without a full Phantom deployment. The selected authentication alert gave the workflow a real indicator from the lab, making the playbook more credible than a purely generic mock example. Wazuh provided detection context, TheHive provided the escalation path, and CrowdSec represented the containment mechanism.

## 13. Strengths of the Playbook
The adapted playbook has several practical strengths:
- it uses a real lab alert instead of an abstract example,
- it provides a repeatable response sequence,
- it reduces uncertainty during first-response handling,
- it integrates detection, escalation, and containment logic,
- it can easily be expanded into a more automated workflow later.

## 14. Limitations
This draft has the following limitations:
- Splunk Phantom was not available in the lab,
- the final TheHive case execution was not captured in the conversation,
- the final CrowdSec decision output was not captured in the conversation,
- the workflow is therefore documented as a tailored SOAR test design based on a real retained alert rather than a fully evidenced end-to-end automation run.
