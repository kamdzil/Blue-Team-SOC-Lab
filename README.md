# Blue Team SOC Lab — Wazuh SIEM & Threat Detection

A hands-on cybersecurity home lab focused on Security Operations Center (SOC) monitoring, detection engineering, threat hunting, and incident response using Wazuh SIEM.

This project demonstrates the deployment of a functional security monitoring environment, Windows endpoint telemetry collection, custom detection rules, and five documented security investigations involving simulated threats.

## Project Highlights

- Deployed Wazuh SIEM on an Ubuntu Server virtual machine using VMware Workstation.
- Integrated a Windows endpoint with Wazuh Agent and Sysmon.
- Collected Windows Security Events, Sysmon process activity, File Integrity Monitoring (FIM) events, and Windows Firewall logs.
- Developed and tested custom detection rules for brute-force authentication attempts and suspicious PowerShell activity.
- Investigated five controlled security scenarios using real alerts and endpoint telemetry.
- Analyzed detection evidence and documented analyst findings, MITRE ATT&CK mappings, and recommended incident response actions.
- Validated configuration restoration and firewall cleanup following simulations.

## Lab Architecture

| Component | Technology | Purpose |
|---|---|---|
| SIEM Server | Ubuntu Server 24.04 LTS | Hosts Wazuh components |
| SIEM Platform | Wazuh 4.7.5 | Event monitoring, correlation, alerting |
| Virtualization | VMware Workstation Pro | Hosts Ubuntu server |
| Endpoint | Windows 11 | Monitored SOC workstation |
| Endpoint Monitoring | Wazuh Agent | Log collection and FIM |
| Process Monitoring | Microsoft Sysmon | Detailed endpoint process telemetry |
| Attack Simulation | Kali Linux | Authorized network reconnaissance |
| Network Analysis | Nmap, Windows Firewall Logs | Network traffic and connection analysis |
| Documentation | GitHub, Markdown | Investigation reporting and evidence |

The Wazuh manager, indexer, and dashboard run on Ubuntu Server. The Windows endpoint forwards security telemetry to Wazuh for centralized investigation.

Kali Linux runs separately and was used for controlled reconnaissance against the Windows endpoint.

## Repository Structure

```text
Blue-Team-SOC-Lab/
├── README.md
├── docs/
│   ├── 01-vmware-setup.md
│   ├── 02-ubuntu-setup.md
│   ├── 03-wazuh-installation.md
│   ├── 04-windows-agent-deployment.md
│   ├── ...
│   ├── 08-brute-force-detection.md
│   └── 09-file-integrity-monitoring.md
└── investigations/
    ├── README.md
    ├── INV01-BruteForce-Investigation.md
    ├── INV02-PowerShell-Investigation.md
    ├── INV03-Phishing-Investigation.md
    ├── INV04-ConfigTampering-Investigation.md
    ├── INV05-Network-Investigation.md
    └── Investigation evidence screenshots
```

## Lab Implementation

The initial build consists of nine documented phases covering installation, configuration, telemetry collection, and security monitoring.

**[View Lab Setup & Implementation](docs/)**

### Key Implementations

**1. SIEM Deployment**

Installed and configured Wazuh Manager, Indexer, and Dashboard on an Ubuntu Server VM.

**2. Windows Endpoint Integration**

Deployed a Windows Wazuh Agent and verified successful communication with the Wazuh Manager.

**3. Windows Security Monitoring**

Enabled Windows authentication auditing and verified ingestion of failed login events.

**4. Sysmon Integration**

Configured Windows Sysmon telemetry collection to monitor process creation and associated command-line activity.

**5. Custom Detection Engineering**

Created and tested Wazuh rules for suspicious authentication behavior and PowerShell execution patterns.

**6. File Integrity Monitoring**

Configured real-time monitoring of selected directories and detected file creation, modification, and deletion events.

**7. Network Monitoring**

Ingested Windows Firewall logs to identify and correlate repeated rejected network connections.

## SOC Investigations

Five documented investigations demonstrate practical detection, investigation, and response workflows.

| # | Investigation | Key Detection | Result |
|---|---|---|---|
| 01 | Brute-Force Login Detection | Custom Rule 100200, Level 12 | Repeated login failures correlated |
| 02 | Suspicious PowerShell Execution | Custom Rule 100300, Level 10 | Execution-policy bypass detected |
| 03 | Phishing Email Analysis | FIM Rule 550, Level 7 | Simulated email modification detected |
| 04 | Security Configuration Tampering | FIM Rules 554 and 550 | Tampering and restoration verified |
| 05 | Network Reconnaissance | Firewall Rule 4151, Level 10 | Repeated dropped connections correlated |

### Investigation Reports

**[01 — Brute-Force Login Detection](investigations/INV01-BruteForce-Investigation.md)**

Simulated failed Windows authentication attempts, investigated Security Event ID 4625, and validated a custom high-severity correlation alert mapped to MITRE ATT&CK T1110.

**[02 — Suspicious PowerShell Execution](investigations/INV02-PowerShell-Investigation.md)**

Analyzed Sysmon process creation telemetry, developed a custom rule detecting `-ExecutionPolicy Bypass`, and validated MITRE ATT&CK T1059.001 mapping.

**[03 — Phishing Email Analysis](investigations/INV03-Phishing-Investigation.md)**

Examined a simulated invoice-themed phishing email, extracted suspicious indicators, calculated SHA-256 hashes, and investigated file modifications using Wazuh FIM.

**[04 — Security Configuration Tampering](investigations/INV04-ConfigTampering-Investigation.md)**

Simulated changes to security configuration values, detected integrity violations, restored the original file, and verified recovery through Wazuh.

**[05 — Network Reconnaissance Detection](investigations/INV05-Network-Investigation.md)**

Performed authorized Nmap reconnaissance from Kali Linux, analyzed Windows Firewall logs, and investigated a high-severity Wazuh alert correlating multiple dropped connections.

**[Browse All Investigations](investigations/README.md)**

## Detection Engineering

The lab includes custom detection rules designed and validated against controlled simulation events.

| Rule ID | Detection | Severity | MITRE ATT&CK |
|---|---|---|---|
| 100200 | Repeated Windows authentication failures | 12 | T1110 |
| 100300 | PowerShell execution-policy bypass | 10 | T1059.001 |

These rules demonstrate practical experience with Wazuh XML rule configuration, correlation conditions, command-line matching, configuration validation, and alert verification.

The existing Wazuh rule set was also used to investigate Sysmon process activity, file integrity changes, and repeated firewall drops.

## Incident Response Workflow

Each investigation follows a structured approach:

1. **Simulation:** Generate controlled, authorized security activity.
2. **Detection:** Identify the relevant Wazuh alert or log event.
3. **Investigation:** Examine source details, timestamps, event metadata, and affected assets.
4. **Analysis:** Determine whether the available evidence supports malicious behavior.
5. **Response:** Document appropriate containment or remediation actions and perform recovery where applicable.
6. **Documentation:** Preserve screenshots, technical findings, and investigation conclusions.

All scenarios were conducted in an authorized home lab. Simulated malicious behavior was distinguished from actual system compromise.

## Skills Demonstrated

- SIEM deployment and administration
- Security event monitoring and log correlation
- SOC alert triage and investigation
- Windows Event Log analysis
- Sysmon endpoint telemetry analysis
- Wazuh custom detection rule development
- File Integrity Monitoring
- Phishing email indicator analysis
- Network reconnaissance detection
- Windows Firewall log analysis
- MITRE ATT&CK mapping
- Incident response and remediation verification
- Technical documentation and evidence collection

## Security and Scope

This project was performed using personally controlled devices and authorized laboratory simulations.

The simulations did not involve unauthorized third-party systems. Temporary firewall rules and logging changes used during network testing were restored after the investigation.

Network addresses in publicly shared investigation materials may be redacted to minimize exposure of the home lab's configuration.

## Project Outcome

Successfully built and tested a functional Wazuh-based SOC home lab and completed five evidence-backed security investigations.

The project demonstrates hands-on experience across the SOC lifecycle, from telemetry collection and detection development to alert analysis, incident documentation, and remediation verification.

**Project Status: Completed — November 2026**
