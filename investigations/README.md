# SOC Security Investigations

This directory contains five hands-on security investigations conducted using a Wazuh-based Security Operations Center (SOC) home lab.

Each investigation includes a controlled security simulation, Wazuh detection evidence, analyst findings, and recommended response procedures.

## Investigation Index

| # | Investigation | Detection | Severity |
|---|---|---|---|
| 01 | [Windows Brute-Force Login Detection](INV01-BruteForce-Investigation.md) | Rule 100200 | Level 12 |
| 02 | [Suspicious PowerShell Execution](INV02-PowerShell-Investigation.md) | Rule 100300 | Level 10 |
| 03 | [Phishing Email Analysis](INV03-Phishing-Investigation.md) | Rule 550 | Level 7 |
| 04 | [Security Configuration Tampering](INV04-ConfigTampering-Investigation.md) | Rules 554 / 550 | Level 5 / 7 |
| 05 | [Network Reconnaissance Detection](INV05-Network-Investigation.md) | Rule 4151 | Level 10 |

## Investigation Summaries

### Investigation 01 — Brute-Force Login Detection

Analyzed repeated Windows authentication failures and verified a custom Wazuh correlation rule.

**Key Evidence:** Windows Event ID 4625, failed login records, high-severity Wazuh alert.

**MITRE ATT&CK:** T1110 — Brute Force

[View Investigation 01](INV01-BruteForce-Investigation.md)

### Investigation 02 — Suspicious PowerShell Execution

Investigated PowerShell process creation, analyzed command-line arguments, and developed a custom execution-policy bypass detection rule.

**Key Evidence:** Sysmon Event ID 1, PowerShell command line, Wazuh Level 10 alert.

**MITRE ATT&CK:** T1059.001 — PowerShell

[View Investigation 02](INV02-PowerShell-Investigation.md)

### Investigation 03 — Phishing Email Analysis

Analyzed a simulated phishing email for suspicious indicators, calculated file hashes, and investigated changes to the stored email artifact.

**Key Evidence:** Email headers, suspicious URL, SHA-256 hashes, Wazuh FIM alert.

**MITRE ATT&CK:** T1565.001 — Stored Data Manipulation (file modification event)

[View Investigation 03](INV03-Phishing-Investigation.md)

### Investigation 04 — Security Configuration Tampering

Simulated security configuration changes, detected file integrity violations, and verified successful restoration.

**Key Evidence:** Modified configuration, Wazuh FIM alerts, hash changes, remediation verification.

**MITRE ATT&CK:** T1565.001 — Stored Data Manipulation

[View Investigation 04](INV04-ConfigTampering-Investigation.md)

### Investigation 05 — Network Reconnaissance Detection

Performed an authorized Nmap scan, examined Windows Firewall logs, and validated Wazuh correlation of repeated dropped connections.

**Key Evidence:** Nmap scan results, firewall records, source and destination information, Wazuh Rule 4151.

[View Investigation 05](INV05-Network-Investigation.md)

## SOC Investigation Methodology

Each investigation follows a consistent workflow:

1. **Detection:** Identify suspicious events or alerts.
2. **Triage:** Review severity, source, and affected system.
3. **Evidence Collection:** Gather relevant logs, process information, hashes, and screenshots.
4. **Analysis:** Evaluate event context and identify the cause.
5. **Response:** Recommend or perform appropriate remediation.
6. **Documentation:** Record findings and final conclusions.

## Evidence and Documentation

Each investigation report includes screenshots collected during the simulation and analysis.

The screenshots document Wazuh dashboard alerts, event details, endpoint telemetry, and other relevant evidence.

All activity was conducted in an authorized laboratory environment. No real-world compromise is claimed.

---

**Investigations Completed:** 5/5  
**SIEM Platform:** Wazuh 4.7.5  
**Completion Date:** November 2026

[Return to Main Project README](../README.md)
