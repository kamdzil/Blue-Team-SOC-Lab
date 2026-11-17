# Investigation 05 — Network Reconnaissance Detection

## Overview

This investigation demonstrates the detection and analysis of network reconnaissance using Kali Linux, Nmap, Windows Firewall logging, and Wazuh SIEM.

**Environment:** Kali Linux, Windows 11, Windows Defender Firewall, Wazuh SIEM, Ubuntu Server  
**Incident Type:** Simulated Network Reconnaissance  
**Severity:** Level 10 (Wazuh Alert)  
**Status:** Closed — Authorized Security Simulation

## 1. Investigation Objective

Conduct an authorized network reconnaissance simulation, identify exposed services, capture firewall telemetry, correlate suspicious network activity in Wazuh, and document the investigation findings.

The investigation demonstrates the SOC workflow:

**Reconnaissance → Log Collection → Detection → Analysis → Remediation**

## 2. Laboratory Environment

| Component | IP Address | Role |
|---|---|---|
| Kali Linux | 192.168.###.### | Scanning system |
| Mac Host | 192.168.#.### | NAT egress |
| Windows Endpoint | 192.168.##.###| Scan target |
| Wazuh Server | 192.168.####.#### | Security monitoring |

The Kali VM used NAT networking. Consequently, Windows Firewall recorded the incoming traffic as originating from the Mac host's IP address, `192.###.##.###`.

All scanning was performed against an authorized laboratory endpoint.

## 3. Network Reconnaissance Simulation

An Nmap TCP SYN scan was performed against the monitored Windows endpoint.

```bash
sudo nmap -sS -Pn -sV --top-ports 100 192.168.##.###
```

### Scan Results

| Finding | Result |
|---|---|
| Target | 192.168.##.### |
| Host Status | Online |
| Open Port | 80/TCP |
| Detected Service | Microsoft IIS HTTP Server 10.0 |
| Filtered Ports | 99 |
| Scan Duration | 8.82 seconds |

The scan identified an HTTP service exposed on port 80 while the remaining 99 tested ports were reported as filtered.

### Evidence — Nmap Results

<img width="1294" height="313" alt="Screenshot 2026-11-16 at 6 58 41 PM" src="https://github.com/user-attachments/assets/596692be-3a18-4e86-8e26-c3d89b4e7f4d" />


## 4. Windows Firewall Log Collection

Windows Defender Firewall logging was temporarily enabled to collect allowed and blocked network connections.

The firewall log was located at:

`C:\Windows\System32\LogFiles\Firewall\pfirewall.log`

The captured records included TCP connection attempts targeting ports 80, 135, 139, and 445.

### Observed Network Indicators

| Attribute | Value |
|---|---|
| Source IP | 192.168.###.### |
| Destination IP | 192.168.##.#### |
| Protocol | TCP |
| Destination Ports | 80, 135, 139, 445 |
| Firewall Actions | ALLOW / DROP |
| Activity | Repeated connection attempts |

The firewall logs recorded multiple dropped TCP SYN packets originating from the scanning system's NAT egress address.

### Evidence — Firewall Logs

<img width="1290" height="397" alt="Screenshot 2026-11-16 at 6 59 14 PM" src="https://github.com/user-attachments/assets/b5a6ec2c-2860-4163-9994-58682767229c" />
## 5. Wazuh Firewall Log Integration

The Windows Wazuh agent was configured to collect firewall logs using the following configuration:

```xml
<localfile>
  <location>C:\Windows\System32\LogFiles\Firewall\pfirewall.log</location>
  <log_format>syslog</log_format>
</localfile>
```

After restarting the Wazuh agent, an additional authorized Nmap scan generated new network events.

Wazuh successfully ingested and decoded the Windows Firewall records.

## 6. Network Intrusion Detection

Wazuh generated a Level 10 alert identifying repeated firewall drops from the same source.

### Detection Details

| Field | Observed Value |
|---|---|
| Agent | Windows-SOC-Agent |
| Wazuh Rule | 4151 |
| Severity | Level 10 |
| Description | Multiple Firewall drop events from same source |
| Decoder | windows-firewall |
| Correlation Frequency | 18 |
| Source IP | 192.168.###.## |
| Destination IP | 192.168.####.# |
| Example Destination Port | 135/TCP |
| Firewall Action | DROP |

This alert demonstrated that Wazuh could correlate multiple rejected connections from a common network source.

### Evidence — Wazuh Network Detection

<img width="1276" height="610" alt="Screenshot 2026-11-16 at 7 00 40 PM" src="https://github.com/user-attachments/assets/1135d70d-0d9a-4195-8758-b66d9bb98bc4" />

## 7. Alert Investigation and Correlation

The alert was expanded in Wazuh to examine its associated network evidence.

The investigation identified repeated dropped TCP connections targeting Windows networking services, including ports 135, 139, and 445.

The `previous_output` field contained additional firewall records supporting the correlated alert.

These events were consistent with the controlled Nmap reconnaissance activity performed from Kali Linux.

### Evidence — Detailed Alert Analysis

<img width="1319" height="1193" alt="0b22699e-134b-4c31-804a-534f5c9df51f" src="https://github.com/user-attachments/assets/a2260d53-1047-43f3-9146-6d342acfabae" />

## 8. Analyst Assessment

The repeated connections to multiple destination ports, followed by a high-severity Wazuh correlation alert, were consistent with network reconnaissance.

The source address was traced to the Mac host running the Kali VM through NAT.

The scan was authorized and performed entirely within the laboratory environment.

**Verdict:** True-positive detection of simulated network reconnaissance behavior. No unauthorized intrusion or successful exploitation was identified.

## 9. Recommended Production Response

During a real network reconnaissance investigation, SOC analysts should:

1. Identify the originating IP address and affected destination systems.
2. Analyze connection patterns, targeted ports, and firewall actions.
3. Correlate firewall alerts with intrusion detection and endpoint telemetry.
4. Determine whether the source belongs to an authorized vulnerability scanner.
5. Investigate successful connections to exposed services.
6. Restrict unauthorized traffic when appropriate.
7. Escalate evidence of exploitation or suspicious post-scan activity.

## 10. Laboratory Cleanup

After completing the investigation, temporary Windows Firewall logging settings were returned to their original values.

The temporary ICMP firewall rule was also removed.

These changes restored the firewall settings modified for the simulation without disabling Windows Firewall.

## 11. Conclusion

This investigation demonstrated network reconnaissance detection through the integration of Kali Linux, Nmap, Windows Firewall, and Wazuh SIEM.

The exercise provided hands-on experience in:

- Network reconnaissance analysis
- Firewall logging and traffic inspection
- SIEM log ingestion and decoding
- Network alert correlation
- Source IP attribution within NAT environments
- Incident investigation and documentation

The investigation was successfully completed in an authorized laboratory environment.
