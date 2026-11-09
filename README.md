# Blue Team SOC Home Lab — Wazuh SIEM

## Project Overview

This project demonstrates the deployment and configuration of a Security Operations Center (SOC) home lab using Wazuh SIEM, Ubuntu Server, VMware Workstation Pro, and Windows 11.

The goal was to build a functional security monitoring environment capable of collecting endpoint logs, identifying suspicious activity, and investigating security events.

I deployed a centralized SIEM server, connected a Windows endpoint, and successfully detected and investigated a simulated failed-login attempt.

## Technologies Used

| Technology | Purpose |
|---|---|
| Wazuh SIEM | Security monitoring, log analysis, and threat detection |
| Ubuntu Server 24.04 LTS | Hosting the Wazuh infrastructure |
| VMware Workstation Pro | Virtualization and lab environment |
| Windows 11 Pro | Monitored endpoint |
| Windows PowerShell | Agent deployment and service management |
| Linux CLI | Server administration and troubleshooting |
| VMware NAT | Virtual network connectivity |

## Lab Architecture

The environment consists of two primary components:

**1. Wazuh SIEM Server**

- Ubuntu Server running in VMware Workstation Pro
- 4 virtual CPU cores
- 8 GB RAM
- 50 GB virtual disk
- Wazuh Manager, Indexer, and Dashboard

**2. Monitored Windows Endpoint**

- Windows 11 Pro
- Wazuh Agent 4.14.10
- Agent name: `Windows-SOC-Agent`
- Centralized security event monitoring

The Windows agent communicates with the Wazuh Manager through VMware's virtual network, forwarding security events for analysis and investigation.

## Project Accomplishments

### 1. SIEM Infrastructure Deployment

- Installed and configured Ubuntu Server inside VMware Workstation Pro.
- Deployed Wazuh Manager, Indexer, and Dashboard.
- Verified that all three Wazuh services were running.
- Configured virtual networking and verified connectivity between the Windows host and Ubuntu server.

### 2. Endpoint Security Monitoring

- Installed and configured a Wazuh agent on Windows 11.
- Registered the endpoint with the Wazuh Manager.
- Verified that the agent successfully connected with an **Active** status.
- Confirmed that Windows security events were being collected and displayed in Threat Hunting.

### 3. Security Event Detection

Performed a controlled failed-login test against the monitored Windows workstation.

Wazuh detected the authentication failure and generated the following alert:

| Detection Field | Result |
|---|---|
| Event | Failed Windows login |
| Windows Event ID | `4625` |
| Wazuh Rule ID | `60122` |
| Severity | Level 5 |
| Logon Type | `2` — Interactive |
| Detection Group | `authentication_failed` |
| Result | Successfully detected and investigated |

The investigation confirmed that Wazuh was successfully collecting authentication events and providing detailed information for security analysis.

## Project Documentation

The complete installation, configuration, verification, and investigation procedures are documented below.

| Phase | Documentation |
|---|---|
| 1 | [VMware Workstation Pro Setup](docs/01-vmware-setup.md) |
| 2 | [Ubuntu Server Setup](docs/02-ubuntu-setup.md) |
| 3 | [Wazuh SIEM Installation](docs/03-wazuh-installation.md) |
| 4 | [Windows Agent Deployment](docs/04-windows-agent-deployment.md) |
| 5 | [Security Monitoring & Threat Detection](docs/05-security-monitoring-threat-detection.md) |

Each guide includes technical explanations, relevant commands, troubleshooting procedures, and supporting screenshots.

## Skills Demonstrated

- SIEM deployment and administration
- Security Operations Center (SOC) fundamentals
- Windows Event Log analysis
- Endpoint monitoring
- Threat detection and alert investigation
- Linux server administration
- VMware virtualization
- Network connectivity troubleshooting
- Technical documentation

## Key Takeaways

This project provided practical experience deploying and operating a SIEM platform rather than relying solely on theoretical cybersecurity concepts.

I gained experience with centralized security monitoring, endpoint log collection, authentication failure detection, and investigating alerts using Windows Event IDs and Wazuh detection rules.

The completed environment provides a foundation for future exercises involving additional endpoints, custom detection rules, and more advanced incident response scenarios.

## Project Status

**Completed — Initial SOC Deployment and Failed-Login Detection**

The Wazuh SIEM environment is operational, the Windows endpoint is actively monitored, and a controlled authentication failure was successfully detected and investigated.

This project was developed as part of my hands-on cybersecurity portfolio.
