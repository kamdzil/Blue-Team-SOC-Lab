# Phase 5 — Security Monitoring and Threat Detection

## Objective

Validate that the Windows endpoint is sending security events to Wazuh and investigate a controlled failed-login attempt.

## Step 1 — Establish a Monitoring Baseline

1. Opened the Wazuh Dashboard and navigated to **Threat Intelligence → Threat Hunting**.
2. Reviewed the **Dashboard** and **Events** tabs.
3. Recorded a baseline of **810 hits** during the selected 24-hour window.
4. Confirmed that `Windows-SOC-Agent` was reporting events to the manager.

**Screenshot — Threat Hunting Baseline (810 Hits)**

<img width="1919" height="910" alt="git1" src="https://github.com/user-attachments/assets/2507dcae-201a-41dd-9597-91f7e69b3d2f" />


## Step 2 — Simulate a Failed Windows Login

To verify that Wazuh could detect suspicious authentication activity, I performed a controlled failed-login test on the monitored Windows machine.

1. Pressed **Windows + L** to lock the Windows workstation.
2. Entered an intentionally incorrect password.
3. Signed in normally afterward.
4. Returned to **Threat Hunting → Events** and refreshed the results.
5. Observed that the total number of events increased from **810 to 823 hits**.

The increase indicated additional activity during the test period. I then investigated the failed-login alert to confirm detection.

**Screenshot — Security Events After Failed Login (823 Hits)**

<img width="1919" height="913" alt="git22" src="https://github.com/user-attachments/assets/39fe3f5b-345f-4c09-a0ca-2865a8d9bf5b" />


## Step 3 — Investigate the Wazuh Security Alert

After identifying the authentication failure, I opened the alert's document details to examine the detection information.

The following details were recorded:

| Field | Value |
|---|---|
| Alert Description | Logon Failure — Unknown user or bad password |
| Wazuh Rule ID | `60122` |
| Severity Level | `5` |
| Event Group | `authentication_failed` |
| Decoder | `windows_eventchannel` |
| MITRE ATT&CK Mapping | `T1531` |

**Screenshot — Wazuh Alert Details and Detection Rule**

<img width="952" height="863" alt="gtit2" src="https://github.com/user-attachments/assets/69038b54-16d0-4ebe-95a6-7797a8232310" />


The MITRE ATT&CK mapping is part of Wazuh's rule metadata. The activity performed in this test was a failed authentication attempt, not an account-removal attack.

## Step 4 — Verify the Windows Security Event

I further investigated the alert by examining the underlying Windows Event Log data.

The following information confirmed the authentication failure:

| Field | Value |
|---|---|
| Windows Event ID | `4625` |
| Logon Type | `2` — Interactive Logon |
| Status | `0xC000006D` |
| Substatus | `0xC0000380` |
| Event Channel | Security |

Windows Event ID `4625` indicates that a logon attempt failed.

Logon Type `2` indicates an interactive login attempt, consistent with the test performed on the Windows workstation.

**Screenshot — Windows Event ID 4625 Verification**

<img width="958" height="860" alt="gt5" src="https://github.com/user-attachments/assets/5ef3ad7d-a4a0-4a84-8dc7-09603c0df7a3" />


## Results

The security monitoring and threat detection test was successful.

The Wazuh SIEM successfully:

- Collected Windows security events through the deployed agent.
- Detected a controlled failed-login attempt.
- Generated a Level 5 security alert using Wazuh Rule `60122`.
- Recorded Windows Security Event ID `4625`.
- Provided detailed event information for security investigation.

This exercise demonstrated how a Security Operations Center (SOC) analyst can identify, investigate, and validate suspicious authentication activity using a SIEM platform.

**Phase 5 Status: Completed**
