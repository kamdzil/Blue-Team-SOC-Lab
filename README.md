# Blue Team SOC Lab — Wazuh SIEM & Threat Detection

A hands-on cybersecurity home lab focused on Security Operations Center (SOC) monitoring, detection engineering, threat hunting, and incident response using Wazuh SIEM.

This project demonstrates the deployment of a functional security monitoring environment, Windows endpoint telemetry collection, custom detection rules, and five documented security investigations involving simulated threats.

## Project Highlights

- Deployed Wazuh SIEM on an Ubuntu Server virtual machine using VMware Workstation.
- Integrated a Windows endpoint with Wazuh Agent and Microsoft Sysmon.
- Collected Windows Security Events, Sysmon process activity, File Integrity Monitoring (FIM) events, and Windows Firewall logs.
- Developed and tested custom detection rules for brute-force authentication attempts and suspicious PowerShell activity.
- Completed nine documented implementation phases and five hands-on SOC investigations.
- Analyzed detection evidence, investigated security alerts, and documented MITRE ATT&CK mappings.
- Demonstrated incident response procedures, configuration restoration, and remediation verification.
- Created technical documentation with supporting screenshots and investigation reports.

## Lab Architecture

| Component | Technology | Purpose |
|---|---|---|
| SIEM Server | Ubuntu Server 24.04 LTS | Hosts Wazuh components |
| SIEM Platform | Wazuh | Event monitoring, correlation, and alerting |
| Virtualization | VMware Workstation Pro | Hosts Ubuntu Server |
| Endpoint | Windows 11 | Monitored SOC workstation |
| Endpoint Monitoring | Wazuh Agent | Log collection and FIM |
| Process Monitoring | Microsoft Sysmon | Detailed endpoint process telemetry |
| Attack Simulation | Kali Linux | Authorized network reconnaissance |
| Network Analysis | Nmap, Windows Firewall Logs | Network traffic and connection analysis |
| Documentation | GitHub, Markdown | Investigation reporting and evidence collection |

The Wazuh Manager, Indexer, and Dashboard run on an Ubuntu Server virtual machine. The Windows endpoint forwards security telemetry to Wazuh for centralized monitoring and investigation.

Kali Linux runs separately and was used to conduct authorized reconnaissance against the Windows endpoint.

## Repository Structure

```text
Blue-Team-SOC-Lab/
│
├── README.md
│
├── docs/
│   ├── 01-vmware-setup.md
│   ├── 02-ubuntu-setup.md
│   ├── 03-wazuh-installation.md
│   ├── 04-windows-agent-deployment.md
│   ├── 05-security-monitoring-threat-detection.md
│   ├── 06-sysmon-integration-threat-investigation.md
│   ├── 07-custom-threat-detection-rules.md
│   ├── 08-brute-force-detection.md
│   └── 09-file-integrity-monitoring.md
│
└── investigations/
    ├── README.md
    ├── INV01-BruteForce-Investigation.md
    ├── INV02-PowerShell-Investigation.md
    ├── INV03-Phishing-Investigation.md
    ├── INV04-ConfigTampering-Investigation.md
    └── INV05-Network-Investigation.md
```

Supporting screenshots are embedded within the corresponding documentation and investigation reports.

---

## Part 1 — Lab Implementation & Documentation

The SOC environment was built through nine documented phases covering infrastructure deployment, endpoint integration, security monitoring, and threat detection engineering.

Each phase includes implementation steps, technical explanations, and supporting evidence.

| Phase | Implementation | Description |
|---|---|---|
| 01 | [VMware Environment Setup](docs/01-vmware-setup.md) | Configured VMware Workstation and created the virtualized SOC environment. |
| 02 | [Ubuntu Server Setup](docs/02-ubuntu-setup.md) | Installed Ubuntu Server and prepared the host for SIEM deployment. |
| 03 | [Wazuh Installation](docs/03-wazuh-installation.md) | Deployed Wazuh Manager, Indexer, and Dashboard. |
| 04 | [Windows Agent Deployment](docs/04-windows-agent-deployment.md) | Installed the Windows Wazuh Agent and established centralized monitoring. |
| 05 | [Security Monitoring & Threat Detection](docs/05-security-monitoring-threat-detection.md) | Configured Windows security auditing and validated security event collection. |
| 06 | [Sysmon Integration & Threat Investigation](docs/06-sysmon-integration-threat-investigation.md) | Integrated Sysmon for detailed process monitoring and security analysis. |
| 07 | [Custom Threat Detection Rules](docs/07-custom-threat-detection-rules.md) | Developed and configured custom Wazuh detection rules for suspicious activity. |
| 08 | [Brute-Force Attack Detection](docs/08-brute-force-detection.md) | Simulated failed authentication attempts and validated detection alerts. |
| 09 | [File Integrity Monitoring](docs/09-file-integrity-monitoring.md) | Monitored file creation, modification, and deletion using Wazuh FIM. |

### Key Technical Implementations

**SIEM Deployment**

Installed and configured Wazuh Manager, Indexer, and Dashboard on an Ubuntu Server virtual machine, establishing a centralized security monitoring environment.

**Windows Endpoint Integration**

Deployed the Windows Wazuh Agent, configured manager communication, and verified that the endpoint was actively transmitting security telemetry.

**Windows Security Auditing**

Configured authentication auditing and verified the collection of failed Windows login events, including Security Event ID 4625.

**Sysmon Integration**

Integrated Sysmon with Wazuh to collect process creation telemetry, command-line arguments, parent process information, and other endpoint activity.

**Custom Detection Engineering**

Implemented custom Wazuh rules for suspicious PowerShell execution and repeated authentication failures.

**File Integrity Monitoring**

Enabled real-time FIM for selected Windows directories and validated detection of file creation, modification, and deletion.

**Network Log Collection**

Integrated Windows Firewall logs with Wazuh to investigate dropped connections and identify patterns consistent with network reconnaissance.

---

## Part 2 — SOC Security Investigations

Following the infrastructure deployment, five controlled security investigations were conducted to evaluate detection capabilities, analyze security events, and document incident response procedures.

Each investigation includes a simulation, alert evidence, analyst findings, and recommended response actions.

### Investigation Summary

| # | Investigation | Key Detection | Result |
|---|---|---|---|
| 01 | [Brute-Force Login Detection](investigations/INV01-BruteForce-Investigation.md) | Custom Rule 100200, Level 12 | Repeated login failures correlated |
| 02 | [Suspicious PowerShell Execution](investigations/INV02-PowerShell-Investigation.md) | Custom Rule 100300, Level 10 | Execution-policy bypass detected |
| 03 | [Phishing Email Analysis](investigations/INV03-Phishing-Investigation.md) | FIM Rule 550, Level 7 | Suspicious email indicators analyzed and file modifications detected |
| 04 | [Security Configuration Tampering](investigations/INV04-ConfigTampering-Investigation.md) | FIM Rules 554 and 550 | Tampering and restoration verified |
| 05 | [Network Reconnaissance Detection](investigations/INV05-Network-Investigation.md) | Firewall Rule 4151, Level 10 | Repeated dropped connections correlated |

### Investigation 01 — Windows Brute-Force Login Detection

Simulated repeated failed Windows authentication attempts and investigated Security Event ID 4625.

A custom Wazuh correlation rule successfully generated a Level 12 alert following multiple authentication failures.

**Key Skills:** Authentication monitoring, alert correlation, SIEM investigation, MITRE ATT&CK T1110.

[View Investigation 01](investigations/INV01-BruteForce-Investigation.md)

### Investigation 02 — Suspicious PowerShell Execution

Investigated PowerShell process creation using Sysmon telemetry and Wazuh.

Developed and validated a custom Level 10 rule identifying execution-policy bypass behavior.

**Key Skills:** Sysmon analysis, command-line investigation, detection engineering, MITRE ATT&CK T1059.001.

[View Investigation 02](investigations/INV02-PowerShell-Investigation.md)

### Investigation 03 — Phishing Email Analysis

Analyzed a simulated phishing email, identified suspicious sender and reply-to information, examined an embedded URL, and calculated file hashes.

Integrated Wazuh File Integrity Monitoring to detect modifications to the stored email artifact.

**Key Skills:** Phishing indicator analysis, SHA-256 hashing, file integrity monitoring, security investigation.

[View Investigation 03](investigations/INV03-Phishing-Investigation.md)

### Investigation 04 — Security Configuration Tampering

Simulated unauthorized-style modifications to security configuration settings and detected file integrity violations using Wazuh.

Restored the original configuration and verified that Wazuh recorded the restoration-related modification.

**Key Skills:** Configuration integrity, threat detection, incident remediation, recovery verification.

[View Investigation 04](investigations/INV04-ConfigTampering-Investigation.md)

### Investigation 05 — Network Reconnaissance Detection

Performed an authorized Nmap scan from Kali Linux against the monitored Windows endpoint.

Collected Windows Firewall logs, integrated them with Wazuh, and investigated a Level 10 alert identifying repeated dropped connections from the same source.

**Key Skills:** Nmap, network security, Windows Firewall analysis, log correlation, network threat investigation.

[View Investigation 05](investigations/INV05-Network-Investigation.md)

**[Browse All SOC Investigation Reports](investigations/README.md)**

---

## Detection Engineering

The lab includes custom Wazuh detection rules designed to identify suspicious security behavior.

| Rule ID | Detection | Severity | MITRE ATT&CK |
|---|---|---|---|
| 100100 | Encoded PowerShell command detection | Level 10 | Not specified in rule |
| 100200 | Repeated Windows authentication failures | Level 12 | T1110 |
| 100300 | PowerShell execution-policy bypass | Level 10 | T1059.001 |

Custom detection engineering involved:

- Configuring Wazuh XML detection rules.
- Matching suspicious command-line patterns.
- Implementing event frequency thresholds.
- Validating rules before activation.
- Testing rules against controlled activity.
- Investigating generated alerts through the Wazuh Dashboard.

Existing Wazuh detection rules were also used to investigate file integrity changes, process execution, and repeated firewall drops.

## Incident Response Methodology

Each investigation followed a structured SOC workflow:

**1. Simulation**

Generate controlled security activity within an authorized laboratory environment.

**2. Detection**

Identify relevant events and alerts using Wazuh SIEM.

**3. Investigation**

Examine event metadata, source information, timestamps, process execution details, and file integrity changes.

**4. Analysis**

Correlate available evidence, identify suspicious patterns, and distinguish simulated activity from actual compromise.

**5. Response and Remediation**

Determine appropriate response procedures and restore modified settings when applicable.

**6. Verification**

Confirm the expected security events and validate remediation outcomes.

**7. Documentation**

Record findings, supporting screenshots, investigation conclusions, and recommendations.

All investigations were performed in a controlled environment, and no unauthorized third-party systems were targeted.

---

## Skills Demonstrated

### Security Operations

- SIEM deployment and administration
- SOC alert triage and investigation
- Security monitoring and threat hunting
- Log analysis and event correlation
- Incident response and remediation
- Technical incident reporting

### Endpoint Security

- Windows Security Event analysis
- Sysmon process monitoring
- PowerShell execution analysis
- File Integrity Monitoring
- SHA-256 file integrity verification
- Endpoint log collection

### Detection Engineering

- Wazuh XML rule configuration
- Custom detection development
- Frequency-based alert correlation
- Command-line pattern matching
- MITRE ATT&CK mapping
- Detection validation and testing

### Network Security

- Nmap network reconnaissance
- Windows Firewall log analysis
- TCP connection analysis
- Network source and destination investigation
- Network alert correlation
- Firewall configuration verification

### Infrastructure and Tools

- Wazuh SIEM
- Ubuntu Server
- VMware Workstation Pro
- Windows 11
- Kali Linux
- Microsoft Sysmon
- Windows PowerShell
- Nmap
- GitHub and Markdown

## Security and Scope

All security simulations were conducted on personally controlled devices within an authorized home laboratory.

The investigations involved controlled test activity rather than real-world attacks or unauthorized access.

Temporary firewall rules and logging configuration changes introduced during network reconnaissance testing were restored after the exercise.

Certain network addresses in publicly shared investigation materials have been redacted to minimize exposure of the laboratory configuration.

## Project Outcome

Successfully deployed a Wazuh-based Security Operations Center home lab and completed:

- **9 documented infrastructure and security monitoring phases**
- **5 evidence-backed security investigations**
- **Custom detection rules for authentication and PowerShell activity**
- **Windows endpoint, Sysmon, FIM, and firewall log integration**
- **Incident investigation, remediation, and verification exercises**

This project demonstrates practical experience across the SOC lifecycle, including security telemetry collection, detection engineering, alert correlation, incident analysis, and technical documentation.

**Project Status: Completed — November 2026**
