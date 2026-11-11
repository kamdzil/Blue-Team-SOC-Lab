# Phase 7 — Custom Threat Detection Rules & PowerShell Investigation

## Project Overview

In this phase, I expanded my Blue Team SOC home lab by creating, testing, and validating a custom detection rule in Wazuh SIEM.

The objective was to detect PowerShell execution involving Base64-encoded commands, a technique that can be used by attackers to obscure their activities.

Using Microsoft Sysmon, Windows PowerShell, and Wazuh, I successfully generated a simulated security event, developed a custom detection rule, and investigated the resulting alert.

## 1. Custom Detection Rule Configuration

I connected to my Ubuntu Wazuh server through SSH from Windows PowerShell.

```powershell
ssh socadmin@192.168.223.128
```

Before modifying the existing configuration, I created a backup of the Wazuh custom rules file.

```bash
sudo cp /var/ossec/etc/rules/local_rules.xml /var/ossec/etc/rules/local_rules.xml.bak
```

I then edited the custom detection rules file:

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```

My initial rule attempted to match Sysmon Event ID 1 directly using PowerShell executable and command-line fields.

During testing, I discovered that Wazuh already provided a built-in detection rule for this activity: **Rule 92057**.

I refined my custom rule to extend the existing detection using `<if_sid>92057</if_sid>`, allowing Wazuh to apply my custom classification when the parent rule matched.

### Final Custom Rule

```xml
<group name="local,sysmon,powershell,">
  <rule id="100100" level="10">
    <if_sid>92057</if_sid>
    <description>Custom SOC Alert: PowerShell executed with an encoded command</description>
    <group>sysmon,powershell,suspicious_execution,</group>
  </rule>
</group>
```

The rule was assigned ID `100100` and severity Level 10.

<img width="948" height="252" alt="71" src="https://github.com/user-attachments/assets/f0f2f379-2504-417b-9a46-bae99b83fa3c" />


## 2. Rule Validation and Deployment

After creating the custom rule, I validated the Wazuh configuration before restarting the manager.

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

The validation completed without errors.

I then restarted the Wazuh Manager:

```bash
sudo systemctl restart wazuh-manager
```

To confirm the manager was operational, I ran:

```bash
sudo systemctl is-active wazuh-manager
```

The service returned `active`, confirming that the manager restarted successfully with the updated detection rules.

## 3. Sysmon Process Monitoring Configuration

During initial testing, the custom detection did not generate an alert.

I investigated the Windows endpoint and verified that Sysmon was running:

```powershell
Get-Service Sysmon64
```

I also confirmed that its Operational event log was enabled:

```powershell
Get-WinEvent -ListLog "Microsoft-Windows-Sysmon/Operational" | Select-Object RecordCount, IsEnabled
```

Inspection of the Sysmon configuration showed that no event filtering rules had been installed.

I created a minimal configuration to enable process creation logging.

```xml
<Sysmon schemaversion="4.90">
  <EventFiltering>
    <ProcessCreate onmatch="exclude" />
  </EventFiltering>
</Sysmon>
```

I saved this configuration as `C:\SOC-Lab\Sysmon\sysmon-config.xml` and applied it using:

```powershell
& "C:\SOC-Lab\Sysmon\Sysmon64.exe" -c "C:\SOC-Lab\Sysmon\sysmon-config.xml"
```

Sysmon returned:

```text
Configuration file validated.
Configuration updated.
```

This enabled the process creation telemetry needed for the detection exercise.

## 4. Simulated PowerShell Activity

To test the detection pipeline, I executed a harmless PowerShell command using Base64 encoding.

```powershell
powershell.exe -EncodedCommand ([Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes('Write-Output "SOC Lab Test"')))
```

The command was designed to print:

```text
SOC Lab Test
```

The simulation used the `-EncodedCommand` argument, which allows PowerShell to execute Base64-encoded command content.

Although this functionality has legitimate administrative uses, it can also appear in malicious scripts and unauthorized command execution.


## 5. Sysmon Event Verification

After configuring Sysmon, I verified that Windows was recording the simulated process activity.

```powershell
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; Id=1; StartTime=(Get-Date).AddMinutes(-10)} | Where-Object {$_.Message -match 'EncodedCommand'} | Select-Object -First 1 -ExpandProperty Message
```

The resulting Sysmon Event ID 1 included:

- Process image: `powershell.exe`
- Command-line argument: `-EncodedCommand`
- Parent process: `powershell.exe`
- Integrity level: High
- Endpoint user: Local Windows user

These details confirmed that process creation events were being captured successfully.

I then verified in Wazuh Threat Hunting that the event was received and processed.

Wazuh initially generated alerts using built-in Rule `92057`, with severity Level 12.

This confirmed that the event pipeline was functional and helped identify the existing rule that my custom detection could extend.

## 6. Custom Rule Detection Results

After refining the custom rule and restarting Wazuh Manager, I repeated the controlled PowerShell simulation.

In the Wazuh dashboard, I navigated to **Threat Hunting → Events** and filtered for:

```text
rule.id:100100
```

Wazuh successfully generated an alert using my custom rule.

### Detection Results

| Field | Result |
|---|---|
| Custom Rule ID | 100100 |
| Severity | Level 10 |
| Endpoint | Windows-SOC-Agent |
| Event Source | Microsoft Sysmon |
| Event Type | Process Creation |
| Detection | Encoded PowerShell execution |
| Status | Successfully detected |

**Screenshot 3 — Custom Rule 100100 Detection**

<img width="1917" height="911" alt="72" src="https://github.com/user-attachments/assets/23297fbb-2719-4be6-b855-abeb3e378240" />

## 7. Security Alert Investigation

I expanded the custom Wazuh alert to examine the event details and understand what triggered the detection.

### Investigation Findings

| Field | Finding |
|---|---|
| Process | `powershell.exe` |
| Execution Argument | `-EncodedCommand` |
| Parent Process | `powershell.exe` |
| Integrity Level | High |
| Wazuh Agent | Windows-SOC-Agent |
| Alert Severity | Level 10 |
| Custom Rule | 100100 |

The alert included the process executable path, Base64-encoded command-line argument, parent process information, and execution context.

This confirmed that a PowerShell process launched another PowerShell instance with an encoded command.

Because I intentionally executed this activity as part of a controlled lab exercise, I classified the alert as a **true-positive detection of authorized simulated activity**, rather than evidence of an actual system compromise.

**Screenshot 4 — Custom Rule Alert Investigation**

<img width="945" height="887" alt="73" src="https://github.com/user-attachments/assets/99f502db-9709-4b8b-a396-a4fdff6b8eb1" />

## 8. Troubleshooting and Lessons Learned

Several issues occurred during the implementation:

**Issue 1 — Custom rule did not initially trigger**

The first version of the rule attempted to match Sysmon event fields directly. Investigation showed that Wazuh already had a built-in rule detecting encoded PowerShell execution.

I updated my custom rule to use the existing Rule 92057 as its parent, resulting in a successful custom alert.

**Issue 2 — Missing process creation telemetry**

Sysmon was installed and running, but its configuration did not provide the process creation events needed for the test.

I applied a minimal Sysmon configuration and verified that Event ID 1 was generated.

**Issue 3 — PowerShell test execution errors**

Some initial attempts to run the encoded PowerShell test failed due to command ordering and variable initialization.

I corrected the execution syntax and verified the simulated activity through Sysmon and Wazuh.

These troubleshooting steps demonstrated the importance of validating each component of the detection pipeline before assuming an alerting failure is caused by the detection rule itself.

## Skills Demonstrated

- Custom SIEM detection rule development
- Wazuh XML rule configuration
- Parent-child detection rule relationships
- Windows Sysmon process monitoring
- PowerShell execution analysis
- Base64-encoded command detection
- SIEM alert validation
- Security event investigation
- Detection engineering troubleshooting
- Linux service administration and SSH

## Key Takeaways

This phase demonstrated how to extend existing Wazuh detection capabilities with custom rules and validate them through controlled security simulations.

I learned to troubleshoot the complete detection workflow, including endpoint telemetry generation, event forwarding, rule matching, alert generation, and investigation.

The successful detection of encoded PowerShell execution provided practical experience with SIEM detection engineering and SOC alert analysis.

## Project Status

**Completed — Custom Detection Rule Development & Validation**

Custom Rule `100100` is deployed and operational. Wazuh successfully detected the simulated encoded PowerShell execution and generated a Level 10 alert, which I investigated and documented.
