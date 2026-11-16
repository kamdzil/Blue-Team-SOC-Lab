# Investigation 02 — Suspicious PowerShell Execution

## Overview

This investigation demonstrates how a SOC analyst can detect and investigate potentially suspicious PowerShell execution using Wazuh SIEM, Windows Sysmon, and custom detection rules.

**Environment:** Windows 11, Sysmon, Wazuh SIEM, Ubuntu Server  
**Incident Type:** Suspicious PowerShell Execution  
**Severity:** High (Wazuh Level 10)  
**MITRE ATT&CK:** T1059.001 — PowerShell  
**Status:** Closed — Authorized Security Simulation

## 1. Investigation Objective

Identify PowerShell processes using potentially suspicious execution parameters, analyze process creation events, develop a custom detection rule, and validate alert generation in Wazuh.

## 2. Attack Simulation

A controlled PowerShell command was executed on the monitored Windows endpoint:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -Command "Write-Output 'SOC-Lab-Detection-Test'"
```

The command launched a child PowerShell process using the `-ExecutionPolicy Bypass` parameter.

Although the command itself was harmless, similar execution parameters may appear in malicious scripts.

## 3. Initial Detection

Windows Sysmon recorded the PowerShell process creation activity.

Wazuh generated an alert identifying PowerShell spawning another PowerShell instance.

| Field | Observed Value |
|---|---|
| Agent | Windows-SOC-Agent |
| Windows Sysmon Event ID | 1 — Process Creation |
| Initial Wazuh Rule | 92027 |
| Initial Severity | Level 4 |
| Process | powershell.exe |
| Parent Process | powershell.exe |
| Integrity Level | High |

<img width="1917" height="915" alt="2inv1" src="https://github.com/user-attachments/assets/467a68c9-2e63-479f-b76f-57122b6e8bc7" />

### Process Analysis

The event details revealed the executable path, parent process, command-line arguments, and execution context.

<img width="955" height="857" alt="3536" src="https://github.com/user-attachments/assets/1f89e4d9-8832-427a-8c74-fca563b016b1" />

## 4. Custom Detection Engineering

A custom Wazuh detection rule was implemented to identify PowerShell execution using the `-ExecutionPolicy Bypass` parameter.

**Custom Rule ID:** 100300  
**Severity:** Level 10  
**MITRE ATT&CK:** T1059.001

The rule was configured to inspect PowerShell command-line arguments associated with existing Wazuh Rule 92027.

After validating the configuration and restarting the Wazuh Manager, the PowerShell simulation was executed again.

### Detection Result

Wazuh successfully generated a Level 10 alert:

**Custom SOC Alert: PowerShell execution policy bypass detected**

<img width="1917" height="941" alt="cstdet" src="https://github.com/user-attachments/assets/a891f6fc-b096-45c8-9a44-b1d211ee31de" />

## 5. Command-Line Investigation

The recorded command line contained the following suspicious execution parameter:

```text
-ExecutionPolicy Bypass
```

This parameter changes the execution-policy behavior for the launched PowerShell process.

While legitimate administrators may use this option, it can also be used during malicious script execution.

The monitored process was confirmed to be a Windows PowerShell executable launched from the standard Windows system directory.

<img width="957" height="868" alt="14228" src="https://github.com/user-attachments/assets/1c2acea8-5d07-457a-9411-7f16953106fc" />

## 6. MITRE ATT&CK Mapping

The custom detection was mapped to:

| Field | Value |
|---|---|
| Technique | T1059.001 |
| Technique Name | PowerShell |
| Tactic | Execution |
| Wazuh Rule | 100300 |
| Severity | Level 10 |

<img width="957" height="210" alt="2inv5" src="https://github.com/user-attachments/assets/5c621735-7b47-4e87-8ac9-0ad10762dd30" />

## 7. Analyst Assessment

The activity was classified as suspicious based on the PowerShell execution parameters and the creation of a child PowerShell process.

The custom rule successfully elevated the detection from a Level 4 process-creation alert to a Level 10 alert.

However, the command was executed intentionally as part of an authorized security simulation and did not perform malicious operations.

**Verdict:** True-positive detection of the configured PowerShell execution pattern; no confirmed compromise.

## 8. Recommended Response

In a production SOC environment, an analyst should:

1. Review the complete PowerShell command line and execution context.
2. Investigate the parent process and associated user account.
3. Correlate related Sysmon events and PowerShell script execution logs.
4. Examine any downloaded scripts, files, or network connections.
5. Determine whether the activity was authorized.
6. Escalate or isolate the endpoint if additional evidence indicates malicious behavior.

## 9. Conclusion

This investigation demonstrated successful monitoring and analysis of PowerShell process creation using Sysmon and Wazuh.

A custom Wazuh detection rule was developed, tested, and validated to identify execution-policy bypass activity.

The exercise demonstrates practical experience in SIEM monitoring, detection engineering, Windows event analysis, incident investigation, and MITRE ATT&CK mapping.
