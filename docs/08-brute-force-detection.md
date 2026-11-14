# Phase 8 — Windows Brute-Force Attack Detection

## Objective

Configure Wazuh SIEM to detect repeated failed Windows login attempts and generate a high-severity security alert using a custom detection rule.

## Lab Environment

- **SIEM:** Wazuh Manager on Ubuntu Server 24.04 LTS
- **Endpoint:** Windows 11
- **Agent:** Windows-SOC-Agent
- **Attack Type:** Simulated brute-force login attempts
- **MITRE ATT&CK:** T1110 — Brute Force

## 1. Generate Failed Login Attempts

First, I verified that Windows auditing was enabled for successful and failed login attempts using `auditpol`.

I then simulated unsuccessful network authentication attempts using PowerShell:

```powershell
1..5 | ForEach-Object { net use \\127.0.0.1\IPC$ /user:.\FakeSOCUser "WrongPassword123!" 2>&1 }
```

Windows returned System Error 1326, indicating invalid credentials.

I confirmed that Windows recorded Security Event ID **4625**, which represents a failed login.

**Screenshot 1 — Windows Failed Login Event**

<img width="1077" height="125" alt="81" src="https://github.com/user-attachments/assets/72a8917a-efea-4830-9f32-df6f8f5efd53" />


## 2. Verify Wazuh Detection

I checked the Wazuh Manager's alert logs and confirmed that the Windows endpoint successfully forwarded the failed login event.

The alert contained:

- **Event ID:** 4625
- **Wazuh Rule:** 60122
- **Severity:** Level 5
- **Target Account:** FakeSOCUser
- **Source IP:** 127.0.0.1

This confirmed successful communication between the Windows agent and Wazuh Manager.

**Screenshot 2 — Wazuh Failed Login Detection**

<img width="937" height="281" alt="82" src="https://github.com/user-attachments/assets/3fbde3de-dce6-444c-a1a6-9d3f3bd559f8" />


## 3. Configure Custom Brute-Force Detection Rule

I created a custom Wazuh correlation rule in:

`/var/ossec/etc/rules/local_rules.xml`

```xml
<group name="local,windows,brute_force,">
  <rule id="100200" level="12" frequency="5" timeframe="60">
    <if_matched_sid>60122</if_matched_sid>
    <description>Custom SOC Alert: Possible Windows brute-force attack - repeated failed logins</description>
    <mitre>
      <id>T1110</id>
    </mitre>
    <group>authentication_failed,brute_force,</group>
  </rule>
</group>
```

The rule was configured to generate a severity-level 12 alert after five failed login events within 60 seconds.

I validated the configuration using:

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

I then restarted the Wazuh Manager.

## 4. Simulate Repeated Failed Logins

I generated additional failed network authentication attempts using PowerShell:

```powershell
1..6 | ForEach-Object { net use \\127.0.0.1\IPC$ /delete /y 2>$null | Out-Null; net use \\127.0.0.1\IPC$ /user:.\FakeSOCUser "WrongPassword$($_)!" 2>&1 | Out-Null; Start-Sleep -Seconds 2 }
```

The events were successfully recorded by Windows and forwarded to Wazuh.

The custom correlation rule triggered as expected.

**Detection Results:**

| Field | Result |
|---|---|
| Rule ID | 100200 |
| Severity | Level 12 |
| Detection | Repeated failed Windows logins |
| MITRE ATT&CK | T1110 — Brute Force |
| Agent | Windows-SOC-Agent |
| Status | Successfully detected |

**Screenshot 3 — Custom Brute-Force Alert**

<img width="958" height="805" alt="83" src="https://github.com/user-attachments/assets/5dcce014-4ad8-4994-a3ab-3340346af866" />


## 5. Verify Alert in Wazuh Dashboard

I opened Wazuh Threat Hunting and searched for:

```text
rule.id:100200
```

The dashboard displayed the high-severity custom alert associated with the Windows endpoint.

**Screenshot 4 — Wazuh Dashboard Detection**

<img width="1917" height="912" alt="84" src="https://github.com/user-attachments/assets/eaefa6e2-f5ef-4967-896a-bba12276612b" />


## Results

Successfully demonstrated:

- Windows failed authentication event collection
- Wazuh agent-to-manager log forwarding
- Custom correlation rule development
- Multiple-event detection within a defined timeframe
- MITRE ATT&CK technique mapping
- High-severity alert generation and dashboard investigation

## Skills Demonstrated

**SIEM Administration | Windows Event Analysis | Threat Detection Engineering | Wazuh Rule Development | Brute-Force Detection | MITRE ATT&CK | SOC Monitoring**

**Phase 8 Status: Completed**
