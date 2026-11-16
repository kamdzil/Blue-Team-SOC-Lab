# Investigation 03 — Phishing Email Analysis & File Integrity Monitoring

## Overview

This investigation demonstrates how a Security Operations Center (SOC) analyst can examine a simulated phishing email, identify suspicious indicators, calculate file hashes, and correlate file modifications with Wazuh SIEM alerts.

**Environment:** Windows 11, Wazuh SIEM, Windows PowerShell, Ubuntu Server  
**Incident Type:** Simulated Phishing Email / File Modification  
**Severity:** Level 7 (Wazuh File Integrity Alert)  
**MITRE ATT&CK:** T1565.001 — Stored Data Manipulation  
**Status:** Closed — Authorized Security Simulation

## 1. Investigation Objective

Analyze a suspicious invoice-themed email, extract potential phishing indicators, establish an original file hash, and use Wazuh File Integrity Monitoring (FIM) to detect modifications to the stored email artifact.

## 2. Phishing Email Analysis

A simulated phishing email was examined in the following location:

`C:\SOC-Phishing\Investigation\Suspicious-Invoice.eml`

The email impersonated a billing department and requested immediate action regarding an outstanding invoice.

### Identified Indicators

| Indicator | Observed Value | Assessment |
|---|---|---|
| Sender | billing@invoice-alert.example | Simulated billing identity |
| Reply-To | payments@billing-review.example | Different domain from sender |
| Subject | URGENT: Outstanding Invoice - Action Required | Uses urgency and pressure |
| Embedded URL | https://invoice-alert.example/verify | Simulated account verification link |
| Requested Action | Verify account immediately | Potential credential-phishing pretext |

The differing sender and reply-to domains, urgent language, and verification request warranted further examination.

The `.example` domains are reserved for documentation and were used exclusively for simulation. No malicious external infrastructure was contacted.

### Evidence — Email Indicators

<img width="671" height="153" alt="3inv1" src="https://github.com/user-attachments/assets/28bf0f6e-a956-435e-bb4a-802a5b6eda26" />


## 3. File Hash Analysis

A SHA-256 hash was calculated to establish an integrity baseline for the email artifact.

```powershell
Get-FileHash "C:\SOC-Phishing\Investigation\Suspicious-Invoice.eml" -Algorithm SHA256
```

**Original SHA-256:**

```text
D2C623042551CE98F229B7B288FDB4F5D39BE13C906886AAAC568DE7E2F2E907
```

**Original file size:** 478 bytes

This hash served as the baseline for identifying subsequent modifications.

### Evidence — Original File Hash

<img width="737" height="156" alt="3inv2" src="https://github.com/user-attachments/assets/ed507846-7e33-41b5-9943-814b821e3c6a" />


## 4. Wazuh File Integrity Monitoring

The phishing investigation directory was added to the Windows Wazuh agent's File Integrity Monitoring configuration:

```xml
<directories realtime="yes">C:\SOC-Phishing\Investigation</directories>
```

After restarting the Wazuh agent, the directory was monitored for changes.

A controlled modification was made to the simulated phishing email to test detection capabilities.

### Detection Results

| Field | Observed Value |
|---|---|
| Agent | Windows-SOC-Agent |
| Wazuh Rule | 550 |
| Rule Description | Integrity checksum changed |
| Severity | Level 7 |
| File | Suspicious-Invoice.eml |
| Event | Modified |
| Monitoring Mode | Realtime |
| Timestamp | November 16, 2026, 17:41:47 |
| MITRE ATT&CK | T1565.001 |

Wazuh successfully generated a file-integrity alert after detecting a checksum change.

### Evidence — Wazuh FIM Detection

<img width="1917" height="911" alt="3inv3" src="https://github.com/user-attachments/assets/1ebca7ae-a6d6-4ab2-a322-12bde50b0dd7" />

## 5. File Modification Investigation

The Wazuh alert provided file integrity information, including the original and modified file sizes and cryptographic hashes.

| Attribute | Before | After |
|---|---|---|
| File Size | 478 bytes | 556 bytes |
| Event | Original baseline | Modified |
| SHA-256 | D2C62304...E2F2E907 | 30FA08A7...7FCC5B5B |

The different SHA-256 values confirm that the file contents changed.

The file size increased by 78 bytes. The change was intentionally generated during the authorized investigation.

### Evidence — File Modification Details

<img width="957" height="867" alt="3inv4" src="https://github.com/user-attachments/assets/fd139917-1197-4803-af99-b052fa444c31" />


## 6. MITRE ATT&CK Mapping

The file integrity event was mapped by Wazuh to:

| Field | Value |
|---|---|
| Technique ID | T1565.001 |
| Technique | Stored Data Manipulation |
| Tactic | Impact |
| Detection Rule | 550 |

This mapping relates to the observed file modification. It does not establish that a phishing campaign or data-manipulation attack occurred.

## 7. Analyst Assessment

The email contained multiple characteristics commonly associated with phishing attempts, including an urgent financial request, a verification link, and differing sender and reply-to domains.

The email was a simulated artifact and was not verified as a real-world phishing message.

Separately, Wazuh File Integrity Monitoring successfully detected an intentional modification to the stored `.eml` file.

**Verdict:** Suspicious simulated email identified; true-positive file modification detection; no confirmed compromise.

## 8. Recommended Response

In a production SOC environment, an analyst should:

1. Preserve the original email and its complete headers.
2. Validate sender authenticity using SPF, DKIM, and DMARC results when available.
3. Investigate the embedded URL using approved, isolated analysis tools.
4. Search email security logs for additional recipients and delivery events.
5. Correlate endpoint activity with potential attachment execution or URL access.
6. Quarantine confirmed malicious messages and block verified malicious indicators.
7. Escalate if credential submission, unauthorized execution, or compromise is confirmed.

## 9. Conclusion

This investigation demonstrated phishing indicator identification, forensic hash collection, and real-time file modification detection using Wazuh SIEM.

The exercise also demonstrated the importance of distinguishing suspicious email characteristics from confirmed endpoint security events.

The investigation provided practical experience in security artifact analysis, threat detection, file integrity monitoring, incident documentation, and MITRE ATT&CK mapping.
