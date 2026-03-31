# Week 4 Workflow

## Overview
This workflow explains the step-by-step execution followed for Week 4. The work was organized strategically so that one main incident scenario could generate evidence, alerts, triage records, analysis results, metrics, and final reporting for the different required activities. Instead of treating each task as a completely separate lab, the workflow used one connected attack-and-response chain and then reused the outputs for the remaining documentation sections.

## Main Strategy
The Week 4 practical work was centered on one core SOC incident scenario:

- Kali Linux acted as the attacker machine
- Metasploitable2 acted as the target/victim
- Wazuh and Elastic were used for detection and log review
- TheHive was used for case creation and triage
- CrowdSec was used for containment or blocking actions
- Google Docs / Notes were used for reporting and summaries

This approach made the workflow more realistic and ensured that screenshots, evidence, and documentation remained consistent across all required tasks.

## Execution Order
The work was completed in the following logical order:

1. Capstone incident execution
2. Alert detection and triage
3. Evidence collection and analysis
4. Threat hunting validation
5. Post-incident analysis
6. Metrics calculation and executive reporting
7. SOAR playbook design
8. Adversary emulation extension if feasible

## Step 1 - Prepare the Lab Environment
Before starting the attack simulation, the required lab components were checked:

- Kali Linux VM reachable on the network
- Metasploitable2 VM online and responding
- Wazuh dashboard accessible
- TheHive platform accessible
- CrowdSec service active if available
- Elastic / relevant logs available for review

At this stage, baseline connectivity between the systems was verified so that attack traffic, detections, and responses could be observed correctly.

## Step 2 - Execute the Main Attack Scenario
The main capstone scenario began with exploitation of a vulnerable service on Metasploitable2 using Metasploit from Kali Linux.

Typical flow:
- Open `msfconsole`
- Load the Samba exploit module
- Set target IP and attacker listener values
- Execute the exploit
- Confirm whether access or exploitation succeeded

The purpose of this step was to generate a real incident that could later be detected, triaged, investigated, contained, and reported.

## Step 3 - Capture Detection in Wazuh
Immediately after the attack, Wazuh was reviewed to identify related alerts.

Actions performed:
- Search alerts around the attack timestamp
- Review rule level, source IP, destination IP, and alert description
- Open alert details for evidence
- Save screenshots of the relevant detections

This step provided the primary detection evidence used later in triage, threat hunting, and the capstone report.

## Step 4 - Create and Document the Incident in TheHive
Once the attack was detected, a case was created in TheHive.

Typical case details included:
- Incident title
- Severity level
- Source IP
- Target IP
- Short incident description
- Initial classification
- MITRE ATT&CK technique reference

This step converted the raw technical alert into a structured SOC case for tracking and response.

## Step 5 - Perform Alert Triage
The alert was then triaged to determine priority, scope, and validity.

Triage tasks included:
- Confirming whether the alert matched the observed attack
- Reviewing IP addresses and affected service
- Assessing severity and business impact
- Checking whether the activity appeared malicious or suspicious
- Recording triage status as open, under investigation, or confirmed incident

If file hashes or related IOCs were available, they were also checked with threat intelligence tools such as VirusTotal when possible.

## Step 6 - Contain the Incident
After confirmation, containment actions were taken.

Possible containment actions included:
- Blocking the attacker IP with CrowdSec
- Isolating the target VM from the network
- Stopping or restricting the vulnerable service
- Verifying that follow-up communication or access attempts were blocked

This step demonstrated practical response and containment, which is a required part of the capstone workflow.

## Step 7 - Collect and Preserve Evidence
Evidence was then collected from the relevant systems.

Examples of evidence:
- Wazuh alert records
- Network connection output
- Process listings
- Authentication logs
- Screenshots of dashboards and case pages
- Exported text/log evidence
- Hash values for preserved files where possible

Chain-of-custody principles were followed by noting:
- what was collected
- when it was collected
- who collected it
- the hash value if applicable

This step supported both the evidence analysis section and the final incident documentation.

## Step 8 - Perform Threat Hunting
After initial response, the same incident was used for a threat hunting exercise.

Hunting process:
- Define a hunting hypothesis
- Search related logs for the attacker IP or related activity
- Review suspicious processes, connections, or repeated events
- Validate whether activity extended beyond the original alert
- Map observations to relevant MITRE ATT&CK techniques

This step allowed proactive analysis beyond basic alert review and supported the Week 4 threat hunting requirement.

## Step 9 - Conduct Post-Incident Analysis
Once the immediate response was complete, post-incident analysis was performed.

Main tasks:
- Build a short timeline of the incident
- Apply the 5 Whys method
- Identify root causes
- Draft lessons learned
- Prepare the content for a Fishbone Diagram

This step focused on understanding why the incident happened, what worked during the response, and what improvements should be made to the environment or process.

## Step 10 - Calculate Security Metrics
Metrics were then derived from the incident timeline.

Metrics used:
- MTTD (Mean Time to Detect)
- MTTR (Mean Time to Respond)
- Dwell Time
- False positive consideration where relevant

The metrics were documented in a simple table or sheet and later summarized for reporting purposes.

## Step 11 - Prepare Executive Reporting
After the technical investigation, a short executive-style summary was prepared for non-technical stakeholders.

The summary included:
- what happened
- what was detected
- what actions were taken
- key impact
- metrics
- recommended improvements

This ensured the technical findings were translated into a management-level format.

## Step 12 - Build the SOAR Playbook Section
Using the same incident, a response playbook was drafted.

Typical playbook flow:
1. Receive alert
2. Check indicator reputation
3. Create case in TheHive
4. Block malicious IP through CrowdSec
5. Notify analyst
6. Document completion status

Even if full automation was not implemented end-to-end, the playbook design documented how automation would reduce manual analyst effort.

## Step 13 - Extend to Adversary Emulation if Feasible
If time and tooling allowed, the workflow was extended with adversary emulation.

Possible extension:
- simulate a related MITRE ATT&CK technique with MITRE Caldera
- verify Wazuh detection
- document gaps in detection coverage

If this step could not be fully completed in the lab, it was documented as a planned or partial extension rather than omitted.

## Step 14 - Organize Deliverables
After all activities were completed, outputs were organized into the Week 4 repository structure:

- `Documentation/PDF/` for final report files
- `Documentation/Notes/` for markdown working notes
- `Documentation/Screenshots/` for proof of execution
- `Workflow/README.md` for this workflow summary

## Deliverable Mapping
The single main incident was reused to populate the required Week 4 sections as follows:

- Threat Hunting Practice -> hunting hypothesis, query results, findings
- SOAR Playbook Development -> playbook logic and response steps
- Post-Incident Analysis -> 5 Whys, lessons learned, metrics summary
- Alert Triage with Automation -> Wazuh alert review and validation
- Evidence Analysis -> evidence list, logs, chain of custody, hashes
- Adversary Emulation Practice -> optional extension with related TTP simulation
- Security Metrics and Executive Reporting -> MTTD, MTTR, dwell time, executive summary
- Capstone Comprehensive SOC Incident Response -> full end-to-end incident workflow

## Conclusion
The Week 4 workflow followed a practical SOC methodology: simulate an attack, detect it, triage it, contain it, preserve evidence, investigate it, calculate metrics, and report the findings. Using one connected incident scenario made the work more realistic, reduced duplication, and ensured consistency across all required documentation and screenshots.

