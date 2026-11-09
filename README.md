# Blue Team SOC Home Lab — Wazuh SIEM

## Project Overview

This project demonstrates the deployment and configuration of a Security Operations Center (SOC) home lab using Wazuh SIEM, Ubuntu Server, and VMware Workstation Pro.

**Objective:** Build a virtual environment to monitor endpoints, collect security logs, detect suspicious activity, and investigate security alerts.

## Phase 1 — SIEM Server Setup ✅

### Step 1: Install VMware Workstation Pro
- Download and install VMware Workstation Pro on a Windows PC.
- Create a new virtual machine for Ubuntu Server.
- Configure the virtual machine's CPU, RAM, storage, and network adapter.

### Step 2: Install Ubuntu Server
- Download the Ubuntu Server ISO.
- Install Ubuntu Server inside VMware.
- Configure the Ubuntu user account and networking.

### Step 3: Install Wazuh SIEM
- Install the Wazuh server components:
  - Wazuh Manager
  - Wazuh Indexer
  - Wazuh Dashboard
- Save the generated administrator credentials securely.

### Step 4: Verify Wazuh Services

Check the Wazuh Dashboard service:

```bash
sudo systemctl status wazuh-dashboard
```

Check the server's IP address:

```bash
hostname -I
```

Check the network configuration:

```bash
ip route
```

### Step 5: Verify Network Connectivity

From Windows Command Prompt:

```cmd
ping 192.168.223.128
```

The Windows host successfully communicated with the Ubuntu virtual machine.

### Step 6: Access the Wazuh Dashboard

Open Firefox on the Windows host and navigate to:

```text
https://192.168.223.128
```

Log in using the Wazuh administrator credentials.

**Result:** Successfully accessed the Wazuh SIEM dashboard.

## Phase 2 — Endpoint Monitoring

*In progress — Windows agent deployment.*

## Phase 3 — Security Log Collection

*Pending.*

## Phase 4 — Threat Detection and Alert Investigation

*Pending.*

## Phase 5 — Results and Lessons Learned

*Pending.*

## Technologies Used

- VMware Workstation Pro
- Ubuntu Server
- Wazuh SIEM
- Windows
- Linux CLI
- Virtual Networking

## Screenshots

Screenshots documenting installation, network connectivity, agent monitoring, and security alerts will be added as the project progresses.

**Security note:** Credentials, private keys, and other sensitive information will not be published.
