# Investigation 04 — Security Configuration Tampering & Remediation

## Overview

This investigation demonstrates how a Security Operations Center (SOC) analyst can detect unauthorized-style configuration changes, investigate file integrity alerts, and verify remediation using Wazuh SIEM.

**Environment:** Windows 11, Wazuh SIEM, Ubuntu Server  
**Incident Type:** Simulated Security Configuration Tampering  
**Severity:** Level 7 (Wazuh FIM Alert)  
**MITRE ATT&CK:** T1565.001 — Stored Data Manipulation  
**Status:** Closed — Authorized Security Simulation

## 1. Investigation Objective

Simulate suspicious changes to a security configuration file, detect the modification using Wazuh File Integrity Monitoring (FIM), analyze the associated security alerts, and restore the original configuration.

The investigation follows the SOC incident response workflow:

**Detection → Investigation → Remediation → Verification**

## 2. Environment and Baseline Configuration

A simulated security configuration file was created on the monitored Windows endpoint.

**File location:**

`C:\SOC-Monitor\security-config.ini`

**Original configuration:**

```ini
[Security]
FirewallEnabled=True
AuditLogging=True
RemoteAccess=False
```

The file was stored in the existing Wazuh-monitored directory, `C:\SOC-Monitor`.

The values were simulated settings and did not control the actual Windows firewall, audit policy, or remote access configuration.

## 3. Simulated Configuration Tampering

A controlled modification was performed to simulate an attacker attempting to weaken security controls.

The following settings were changed:

| Security Setting | Original Value | Modified Value |
|---|---|---|
| FirewallEnabled | True | False |
| AuditLogging | True | False |
| RemoteAccess | False | False |

The resulting configuration was:

```ini
[Security]
FirewallEnabled=False
AuditLogging=False
RemoteAccess=False
```

Although no actual Windows security controls were disabled, the simulated changes represented potentially dangerous configuration tampering.

### Evidence — Configuration Changes

<img width="1297" height="305" alt="4inv1" src="https://github.com/user-attachments/assets/1de21f8e-db1a-4f85-92f7-5855da2f63b1" />

## 4. Wazuh File Integrity Detection

Wazuh File Integrity Monitoring detected both the creation and modification of the configuration file.

### Detection Results

| Event | Wazuh Rule | Severity | Description |
|---|---|---|---|
| File Created | 554 | Level 5 | File added to the system |
| File Modified | 550 | Level 7 | Integrity checksum changed |

**Monitored Agent:** Windows-SOC-Agent  
**Monitoring Mode:** Realtime  
**File:** `C:\SOC-Monitor\security-config.ini`

Wazuh generated a Level 7 integrity alert when the simulated security settings were modified.

### Evidence — Wazuh Detection

<img width="1917" height="910" alt="4inv2" src="https://github.com/user-attachments/assets/9483e107-bdb8-4c1c-909e-ce6014191047" />

## 5. File Integrity Investigation

The modification alert provided forensic evidence showing the file had changed.

### Key Findings

| Attribute | Before | After |
|---|---|---|
| File Size | 73 bytes | 75 bytes |
| SHA-256 | 82D951BE...D126745 | 5A9E0438...F00901D |
| Monitoring Mode | Realtime | Realtime |

**Modification timestamp:** November 16, 2026, 18:12:56

The difference in SHA-256 values confirmed that the file contents had changed.

The file size increased by two bytes, consistent with the simulated configuration modifications.

### Evidence — File Integrity Details

<img width="953" height="892" alt="4inv3" src="https://github.com/user-attachments/assets/bd7cda8b-7be3-489b-be13-9fbf9e98c02e" />

## 6. MITRE ATT&CK Mapping

Wazuh associated the file modification event with the following MITRE ATT&CK technique:

| Field | Value |
|---|---|
| Technique ID | T1565.001 |
| Technique | Stored Data Manipulation |
| Tactic | Impact |
| Detection Rule | 550 |

The mapping describes the observed modification of stored data. It does not establish that a real attacker modified the file.

## 7. Remediation and Recovery

After investigating the alert, the simulated security configuration was restored to its original state.

**Restored configuration:**

```ini
[Security]
FirewallEnabled=True
AuditLogging=True
RemoteAccess=False
```

The restored configuration was verified directly using Windows PowerShell.

### Evidence — Configuration Restoration

<img width="1296" height="168" alt="4inv4" src="https://github.com/user-attachments/assets/2dec5366-ce49-4589-af4d-cfc17c8dbe29" />

## 8. Remediation Verification in Wazuh

Following restoration, Wazuh generated another file integrity alert.

The event timeline contained three alerts:

| Event | Rule | Description |
|---|---|---|
| Initial Creation | 554 | File added |
| Simulated Tampering | 550 | Integrity checksum changed |
| Configuration Restoration | 550 | Integrity checksum changed |

The final event confirmed that Wazuh detected the restoration-related modification. The restored configuration values were independently verified through PowerShell.

### Evidence — Remediation Verification

<img width="1917" height="908" alt="4inv5" src="https://github.com/user-attachments/assets/b44df877-271b-4169-8d4a-ff1c297f1fc1" />

## 9. Analyst Assessment

Wazuh successfully identified changes to a monitored security configuration file and generated alerts for the initial creation, simulated tampering, and restoration.

The investigation demonstrated how file integrity monitoring can help identify configuration changes that may indicate malicious activity.

The activity was intentionally performed in an authorized laboratory environment.

**Verdict:** True-positive detection of simulated configuration tampering; configuration restored successfully; no actual system compromise.

## 10. Recommended Production Response

In a production environment, a SOC analyst should:

1. Identify the modified configuration file and affected endpoint.
2. Compare the current file against an approved configuration baseline.
3. Investigate the user account and process responsible for the modification.
4. Correlate file changes with Windows security logs and endpoint telemetry.
5. Restore unauthorized changes using approved recovery procedures.
6. Verify that the affected security controls are operating correctly.
7. Monitor for repeated modifications and escalate suspicious activity.

## 11. Conclusion

This investigation demonstrated an end-to-end security monitoring and response workflow using Wazuh SIEM.

By detecting simulated configuration tampering, analyzing file integrity evidence, restoring the original configuration, and verifying the recovery, the exercise demonstrated practical experience in:

- Security monitoring and threat detection
- File integrity analysis
- Incident investigation
- Configuration remediation
- MITRE ATT&CK mapping
- Incident documentation

The investigation was completed successfully within a controlled SOC laboratory environment.
