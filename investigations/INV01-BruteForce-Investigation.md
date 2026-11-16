# Investigation 01 — Windows Brute-Force Login Detection

## Overview
This investigation demonstrates how a Security Operations Center (SOC) analyst can identify and investigate repeated failed Windows authentication attempts using Wazuh SIEM.

**Environment:** Windows 11 endpoint, Wazuh SIEM, Sysmon, Ubuntu Server  
**Incident Type:** Suspected brute-force authentication activity  
**Severity:** High (Wazuh Level 12)  
**MITRE ATT&CK:** T1110 — Brute Force  
**Status:** Closed — Authorized Security Simulation

## 1. Investigation Objective
Detect repeated failed Windows login attempts, analyze the associated security events, verify custom Wazuh correlation rules, and determine whether malicious activity occurred.

## 2. Detection and Analysis
A controlled simulation generated repeated failed Windows authentication attempts using the `net use` command with invalid credentials.

Wazuh recorded nine relevant alerts:

- Eight individual authentication failure alerts (Rule 60122, Level 5)
- One correlated brute-force alert (Custom Rule 100200, Level 12)

The custom rule was configured with a frequency threshold of five failed authentication events.

### Key Evidence

| Field | Observed Value |
|---|---|
| Endpoint | Windows-SOC-Agent |
| Windows Event ID | 4625 |
| Source IP | 127.0.0.1 |
| Target Account | FakeSOCUser5 |
| Authentication Protocol | NTLM |
| Logon Type | 3 — Network |
| Wazuh Correlation Rule | 100200 |
| Alert Severity | Level 12 |
| Detection Timestamp | November 16, 2026, 16:45:43 |
| MITRE ATT&CK | T1110 — Brute Force |

## 3. Evidence Screenshots

### Repeated Authentication Failures
<img width="1917" height="941" alt="inv1" src="https://github.com/user-attachments/assets/648cd32d-a90c-4267-becf-edc15a48db7e" />


### Windows Authentication Event Details
<img width="957" height="867" alt="inv2" src="https://github.com/user-attachments/assets/d543dbb2-8f88-429d-84ed-4136859f08cb" />


### MITRE ATT&CK Mapping
<img width="958" height="303" alt="inv3" src="https://github.com/user-attachments/assets/297b4137-dbe2-40f6-97ac-9c4ee0dfa5dc" />


## 4. Analyst Assessment
The repeated authentication failures triggered a custom high-severity Wazuh correlation alert consistent with a possible brute-force attack.

The source address `127.0.0.1` confirms that the authentication attempts originated locally. The activity was generated intentionally in an authorized laboratory environment.

**Verdict:** True-positive detection of simulated brute-force behavior; no actual compromise identified.

## 5. Recommended Response
In a production environment, the following actions would be appropriate:

1. Investigate the originating endpoint and associated user accounts.
2. Correlate failed authentication events with successful logins.
3. Check for additional authentication attempts across other systems.
4. Review account lockout and authentication protection policies.
5. Escalate to incident response if unauthorized activity is confirmed.

## 6. Conclusion
Wazuh successfully detected repeated Windows authentication failures and generated a high-severity alert using a custom correlation rule. This investigation demonstrates security event analysis, SIEM alert correlation, and MITRE ATT&CK mapping within a controlled SOC environment.
