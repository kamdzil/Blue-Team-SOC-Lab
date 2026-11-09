# Phase 3 — Wazuh SIEM Installation

## Objective

Install and configure Wazuh SIEM on Ubuntu Server to create a centralized security monitoring environment for collecting logs, detecting threats, and investigating security incidents.

## Step 1 — Prepare Ubuntu Server

Update system packages:

```bash
sudo apt update
sudo apt upgrade -y
```

Install curl if needed:

```bash
sudo apt install curl -y
```

## Step 2 — Install Wazuh SIEM

Download the Wazuh installation assistant from the official Wazuh repository.

Example for Wazuh 4.14 (verify the installed version before documenting it as the lab version):

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
```

Run the all-in-one installation:

```bash
sudo bash ./wazuh-install.sh -a
```

The installation includes three primary components:

- **Wazuh Manager:** Processes security events and generates alerts.
- **Wazuh Indexer:** Stores and indexes security data.
- **Wazuh Dashboard:** Provides a web interface for monitoring and investigating security activity.

## Step 3 — Verify Wazuh Services

Check the Wazuh Manager:

```bash
sudo systemctl status wazuh-manager
```

Check the Wazuh Indexer:

```bash
sudo systemctl status wazuh-indexer
```

Check the Wazuh Dashboard:

```bash
sudo systemctl status wazuh-dashboard
```

Expected output:

```text
Active: active (running)
```

**Verified result:** The Wazuh Dashboard service was active and running.

The Manager and Indexer commands are included for additional verification.

Press `Q` to exit the service status screen.

## Step 4 — Verify Network Configuration

Identify the Ubuntu Server's IP address:

```bash
hostname -I
```

Example:

```text
192.168.xxx.xxx
```

Check the routing configuration:

```bash
ip route
```

The virtual machine uses VMware NAT networking and receives its IP address through DHCP.

**Note:** Actual IP addresses are excluded from this public documentation.

## Step 5 — Test Network Connectivity

On the Windows host, open Command Prompt:

```cmd
ping <VM_IP_ADDRESS>
```

Replace `<VM_IP_ADDRESS>` with the Ubuntu Server's assigned IP address.

**Result:** The Windows host successfully communicated with the Ubuntu virtual machine.

## Step 6 — Retrieve Administrator Credentials

The Wazuh installation assistant generates credentials during installation.

To view the generated credentials locally:

```bash
sudo tar -xOf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```

Locate the credentials for the `admin` account.

### Security Considerations

- Never publish passwords or authentication tokens.
- Do not upload `wazuh-install-files.tar`.
- Do not expose private keys or certificates containing sensitive information.
- Redact credentials from screenshots.

## Step 7 — Access the Wazuh Dashboard

Open Firefox on the Windows host.

Navigate to:

```text
https://<VM_IP_ADDRESS>
```

Log in using the Wazuh administrator credentials.

A browser certificate warning may appear if the dashboard uses a self-signed certificate. In this isolated lab environment, verify the destination before accepting a certificate exception.

**Result:** Successfully authenticated and accessed the Wazuh Dashboard.

## Step 8 — Troubleshooting

During initial setup, the Wazuh Dashboard failed to load when accessed from a separate Mac computer.

The following troubleshooting steps were performed:

1. Verified that the Wazuh Dashboard service was running.
2. Confirmed the Ubuntu virtual machine's IP address.
3. Inspected the routing table.
4. Tested network connectivity using `ping`.
5. Identified that the Ubuntu virtual machine was hosted on a Windows PC using VMware Workstation Pro.
6. Successfully tested connectivity from the Windows host.
7. Accessed the Wazuh Dashboard through Firefox on Windows.

### Troubleshooting Outcome

The issue was related to connectivity between the Mac and the VMware virtual network, rather than the Wazuh Dashboard service.

Accessing the dashboard from the Windows host resolved the immediate problem.

## Results

Successfully deployed the Wazuh SIEM environment on Ubuntu Server.

Completed milestones:

- Installed the Wazuh platform.
- Verified the Wazuh Dashboard service.
- Confirmed VMware NAT networking.
- Verified Windows-to-Ubuntu connectivity.
- Accessed the Wazuh Dashboard through HTTPS.
- Successfully authenticated using the administrator account.

## Screenshots

Screenshots to include where available:

- Wazuh installation output
- Wazuh service status
- Ubuntu network configuration
- Successful Windows ping test
- Wazuh Dashboard login page
- Wazuh Dashboard after authentication

## Next Phase — Windows Agent Deployment

Install the Wazuh agent on a Windows endpoint to begin collecting security logs and monitoring endpoint activity.

The next phase will cover:

- Windows agent installation
- Agent registration with the Wazuh Manager
- Endpoint connectivity verification
- Security log collection
- Initial security monitoring

**Project Status:** Wazuh server installation complete. Endpoint monitoring pending.
