# Phase 9 — File Integrity Monitoring (FIM)

## Objective

Configure Wazuh SIEM to monitor a Windows directory for unauthorized file creation, modification, and deletion, and investigate the resulting security alerts.

## Lab Environment

- **SIEM:** Wazuh Manager on Ubuntu Server 24.04 LTS
- **Endpoint:** Windows 11
- **Agent:** Windows-SOC-Agent
- **Monitored Directory:** `C:\SOC-Monitor`
- **Monitoring Method:** Real-time File Integrity Monitoring (FIM)

## 1. Configure File Integrity Monitoring

First, I created a dedicated directory on the Windows endpoint using PowerShell:

```powershell
New-Item -Path "C:\SOC-Monitor" -ItemType Directory -Force
```

I then modified the Wazuh agent configuration file:

`C:\Program Files (x86)\ossec-agent\ossec.conf`

Inside the `<syscheck>` section, I added:

```xml
<directories check_all="yes" realtime="yes">C:\SOC-Monitor</directories>
```

This configuration enabled real-time monitoring for changes to files within the specified directory.

After saving the configuration, I restarted the Wazuh agent and verified that its service was running.

The agent log confirmed that `C:\SOC-Monitor` was being monitored.

## 2. Detect File Creation

I created a test file in the monitored directory:

```powershell
Set-Content -Path "C:\SOC-Monitor\test-file.txt" -Value "SOC File Integrity Monitoring Test"
```

Wazuh successfully detected the new file.

**Detection Details:**

- **Rule ID:** 554
- **Severity:** Level 5
- **Event:** File added
- **File:** `C:\SOC-Monitor\test-file.txt`

## 3. Detect File Modification

Next, I simulated an unauthorized file modification:

```powershell
Add-Content -Path "C:\SOC-Monitor\test-file.txt" -Value "Unauthorized modification simulation"
```

I performed an additional modification to verify detection.

Wazuh detected the change to the file's integrity checksum.

**Detection Details:**

- **Rule ID:** 550
- **Severity:** Level 7
- **Event:** File modified
- **Description:** Integrity checksum changed

**Screenshot 1 — File Creation and Modification Evidence**

<img width="947" height="95" alt="12-9" src="https://github.com/user-attachments/assets/33bc8923-1424-4f5c-8a10-1bc75fb2d01f" />

## 4. Detect File Deletion

I deleted the monitored file:

```powershell
Remove-Item "C:\SOC-Monitor\test-file.txt"
```

Wazuh generated a file deletion alert.

**Detection Details:**

- **Rule ID:** 553
- **Severity:** Level 7
- **Event:** File deleted

I verified all three events in the Wazuh Manager's alert logs.

**Screenshot 2 — All File Integrity Events**

<img width="932" height="136" alt="alle" src="https://github.com/user-attachments/assets/ea45847a-a930-4d0c-a475-9a1c76932c35" />


## 5. Investigate Alerts in Wazuh Dashboard

I opened the Wazuh Threat Hunting interface and searched for:

```text
syscheck.path:*
```

The dashboard displayed all three file integrity events.

| Activity | Rule ID | Severity | Result |
|---|---|---|---|
| File creation | 554 | 5 | Detected |
| File modification | 550 | 7 | Detected |
| File deletion | 553 | 7 | Detected |

All alerts originated from the `Windows-SOC-Agent` endpoint.

**Screenshot 3 — Wazuh FIM Dashboard**

<img width="1917" height="912" alt="9dash" src="https://github.com/user-attachments/assets/2e142027-4a3b-4dca-9803-9787aa32aaa4" />


## 6. Security Analysis

File Integrity Monitoring helps identify suspicious changes to monitored files and directories.

Potential security applications include:

- Detecting unauthorized modifications to configuration files
- Monitoring sensitive directories for unexpected changes
- Identifying file deletion or tampering
- Supporting incident investigations through file activity logs
- Detecting changes that may indicate malicious activity

These tests used controlled file operations rather than actual malware or unauthorized access.

## Results

Successfully configured and validated real-time FIM on a Windows 11 endpoint.

Wazuh detected file creation, modification, and deletion, and displayed the corresponding alerts in its dashboard.

## Skills Demonstrated

**Wazuh SIEM | File Integrity Monitoring | Windows Security | Endpoint Monitoring | Log Analysis | Security Alert Investigation | SOC Operations**

**Phase 9 Status: Completed**
