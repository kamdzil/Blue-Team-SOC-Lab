# Phase 10 — SOC Phishing Incident Investigation

## Objective

Simulate a phishing email investigation from the perspective of a Security Operations Center (SOC) analyst.

The goal was to examine suspicious email indicators, investigate endpoint activity using Sysmon and Wazuh SIEM, assess the potential security impact, and document an appropriate incident response.

## Lab Environment

- **SIEM:** Wazuh on Ubuntu Server 24.04 LTS
- **Endpoint:** Windows 11
- **Monitoring Agent:** Windows-SOC-Agent
- **Endpoint Telemetry:** Sysmon
- **Investigation Tools:** PowerShell, Windows Event Logs, Wazuh
- **Case ID:** PHISH-001
- **Investigation Type:** Simulated phishing email
- **Date:** November 14, 2026

## 1. Incident Scenario

An employee reports receiving a suspicious email claiming to be from a billing department.

The email warns that an invoice is overdue and instructs the recipient to immediately verify their account through an external link.

As the SOC analyst, my responsibility was to determine whether the email exhibited phishing characteristics and whether any related suspicious activity occurred on the Windows endpoint.

This was a controlled simulation using fictional email addresses and domains.

## 2. Email Analysis

I created a simulated email file named `Suspicious-Invoice.eml` and examined its contents using PowerShell.

```powershell
Get-Content "C:\SOC-Phishing\Investigation\Suspicious-Invoice.eml"
```

The email contained the following information:

| Field | Value |
|---|---|
| Sender | billing@invoice-alert.example |
| Recipient | employee@company.example |
| Reply-To | payments@billing-review.example |
| Subject | URGENT: Outstanding Invoice - Action Required |
| URL | https://invoice-alert.example/verify |

Several phishing indicators were identified:

- The sender and reply-to addresses used different domains.
- The subject line created a sense of urgency.
- The message threatened service suspension.
- The recipient was instructed to verify account information through an external link.
- The email contained no verifiable business context establishing its legitimacy.

The `.example` domains were intentionally fictional and were not live malicious infrastructure.

**Screenshot 1 — Suspicious Email Analysis**

<img width="1098" height="322" alt="16" src="https://github.com/user-attachments/assets/51ad44b2-c35e-4a77-ab53-d93a6b38639d" />

## 3. Indicator of Compromise (IOC) Extraction

I extracted relevant email indicators using PowerShell:

```powershell
Select-String -Path "C:\SOC-Phishing\Investigation\Suspicious-Invoice.eml" -Pattern 'From:|Reply-To:|Subject:|https?://'
```

This identified the sender, reply-to address, subject, and suspicious URL.

These values were treated as indicators for investigation rather than confirmed malicious IOCs, since the email was simulated.

**Screenshot 2 — Email Indicator Extraction**

<img width="1126" height="158" alt="17" src="https://github.com/user-attachments/assets/b7234405-94a1-472b-b380-bf685b6133f7" />

## 4. Endpoint Process Investigation

During a separate controlled attachment-execution test, I used a harmless PowerShell script named `Invoice-Review.ps1` to simulate suspicious attachment execution.

Sysmon recorded the process activity as Event ID 1.

The recorded information included:

- **Process:** powershell.exe
- **Event ID:** 1 — Process Creation
- **Execution Policy:** Bypass
- **Script:** C:\SOC-Phishing\Invoice-Review.ps1
- **Integrity Level:** High
- **Parent Process:** powershell.exe

**Screenshot 3 — Sysmon Process Creation Evidence**

<img width="1127" height="558" alt="15" src="https://github.com/user-attachments/assets/cfb2fc44-c1b3-4789-9c5f-3dd1d1cf3ea4" />

This demonstrated Sysmon's ability to record potentially suspicious PowerShell execution.

The script was intentionally executed for testing and was not delivered through the simulated email. Its execution was therefore documented separately from the email's impact assessment.

## 5. Investigate DNS Activity

I searched Sysmon Event ID 22 for DNS queries involving the simulated phishing domain.

```powershell
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational';Id=22;StartTime=(Get-Date).AddHours(-2)} -ErrorAction SilentlyContinue | Where-Object {$_.Message -match 'invoice-alert\.example'} | Select-Object TimeCreated,Id,Message | Format-List
```

**Result:** No matching DNS events were found during the examined period.

This was consistent with the simulated phishing URL not being accessed, but the absence of matching logs did not conclusively establish that no access occurred.

## 6. Investigate Wazuh SIEM Alerts

I searched Wazuh for security alerts involving the suspicious domain, including relevant DNS query and process command-line fields.

The investigation focused on events associated with `Windows-SOC-Agent`.

**Result:** No matching Wazuh alerts were found for the simulated phishing domain.

This indicated that no related activity was observed in the matching alert records examined.

## 7. Investigate File Activity

I examined Sysmon Event ID 11, which records file-creation activity, for relevant filenames and download locations.

```powershell
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational';Id=11;StartTime=(Get-Date).AddHours(-2)} -ErrorAction SilentlyContinue | Where-Object {$_.Message -match 'Downloads\\|invoice-alert|Invoice-Review'} | Select-Object -First 10 TimeCreated,Id,Message | Format-List
```

**Result:** No matching file-creation events were returned.

This did not prove that no files had been created, only that the monitored records and search criteria produced no relevant matches.

## 8. SOC Incident Assessment

The investigation findings were summarized in PowerShell.

| Investigation Category | Finding |
|---|---|
| Case ID | PHISH-001 |
| Suspicious email | Confirmed in simulation |
| Sender/Reply-To mismatch | Identified |
| Urgent verification request | Identified |
| Matching phishing-domain DNS events | 0 observed |
| Matching Wazuh alerts | 0 observed |
| Related file-creation events | 0 observed |
| Confirmed endpoint compromise | None established |
| Case classification | Simulated phishing attempt |

**Screenshot 4 — SOC Investigation Summary**

<img width="1131" height="136" alt="18" src="https://github.com/user-attachments/assets/e95a27f6-4477-4c32-bf17-5f1566ccf3df" />

## 9. Recommended Incident Response

Based on the findings, I would recommend the following actions in a real SOC environment:

1. **Email containment:** Quarantine the reported email and search for similar messages across affected mailboxes.
2. **Indicator validation:** Verify the actual sender domains, URLs, and attachments using appropriate threat intelligence sources before implementing broad blocks.
3. **User protection:** Instruct the recipient not to interact with suspicious links or attachments and verify whether credentials were submitted.
4. **Endpoint investigation:** Review additional endpoint, browser, proxy, DNS, and email security telemetry as available.
5. **Remediation:** If malicious activity is confirmed, isolate affected systems, revoke compromised sessions, reset affected credentials, and remove malicious artifacts as appropriate.
6. **Documentation:** Record evidence, investigative findings, decisions, and unresolved visibility gaps in the incident ticket.

## 10. Final SOC Analyst Conclusion

The simulated email exhibited multiple common phishing characteristics, including mismatched sender and reply-to domains, urgency-based messaging, and an external account-verification link.

The investigation found no matching phishing-domain DNS events, Wazuh alerts, or relevant file-creation events within the examined records.

A separate controlled PowerShell execution test successfully demonstrated Sysmon process monitoring, but it did not establish compromise from the simulated email.

**Final assessment:** Suspicious simulated email with no confirmed endpoint compromise based on the available evidence.

In a real incident, further user verification and review of available email and network telemetry would be necessary before concluding that the threat had been fully contained.

## Skills Demonstrated

**SOC Alert Triage | Phishing Email Analysis | IOC Extraction | Windows Event Logs | Sysmon | Wazuh SIEM | Endpoint Investigation | Incident Response | Security Documentation**

**Phase 10 Status: Completed — Simulated SOC Investigation**
