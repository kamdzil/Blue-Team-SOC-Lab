# Phase 2 — Ubuntu Server Setup

## Objective

Install and configure Ubuntu Server inside VMware Workstation Pro to provide a stable Linux environment for hosting Wazuh SIEM.

## Step 1 — Install Ubuntu Server

1. Power on the virtual machine in VMware Workstation Pro.
2. Boot using the Ubuntu Server ISO.
3. Select the preferred language and keyboard layout.
4. Configure the network using DHCP.
5. Configure the storage using the previously allocated **50 GB virtual disk**.
6. Create an administrator username and password.
7. Complete the Ubuntu Server installation.
8. Restart the virtual machine.

**Security Note:** Never publish actual passwords or administrator credentials on GitHub.

## Step 2 — Log In to Ubuntu

After restarting the virtual machine:

1. Enter the username created during installation.
2. Enter the account password.
3. Confirm that the Ubuntu terminal appears.

Ubuntu Server uses a command-line interface for system administration.

## Step 3 — Update System Packages

Refresh the package repositories:

```bash
sudo apt update
```

Upgrade installed packages:

```bash
sudo apt upgrade -y
```

These commands help ensure that the operating system receives available package and security updates.

## Step 4 — Verify the Operating System

Check the Ubuntu version:

```bash
lsb_release -a
```

Check system resources:

```bash
free -h
```

Check available disk space:

```bash
df -h
```

These commands help confirm the operating system version, memory allocation, and disk availability.

## Step 5 — Verify Network Configuration

Identify the virtual machine's IP address:

```bash
hostname -I
```

Example:

`192.168.xxx.xxx`

Inspect network interfaces:

```bash
ip addr
```

View the routing table:

```bash
ip route
```

The virtual machine uses VMware NAT networking with an automatically assigned private IP address.

**Note:** IP addresses shown in this documentation are placeholders.

## Step 6 — Test Network Connectivity

From Windows Command Prompt, run:

```cmd
ping <VM_IP_ADDRESS>
```

Replace `<VM_IP_ADDRESS>` with the IP address assigned to the Ubuntu virtual machine.

**Result:** The Windows host successfully communicated with the Ubuntu Server VM.

## Step 7 — Verify Administrative Access

Confirm that the Ubuntu account can execute administrative commands:

```bash
sudo whoami
```

Expected output:

```text
root
```

This verifies that the account has appropriate administrative privileges.

## Results

Ubuntu Server was successfully installed and configured within VMware Workstation Pro.

The environment provides:

- A functioning Linux server
- VMware NAT networking
- Administrative terminal access
- Network connectivity with the Windows host
- A foundation for Wazuh SIEM deployment

## Screenshots

The following screenshots will be added where available:

- Ubuntu Server installation
- Ubuntu terminal after successful login
- Operating system version
- Network interface configuration
- Windows-to-Ubuntu connectivity test

## Next Phase

**Phase 3 — Wazuh SIEM Installation**

Document the installation of the Wazuh Manager, Indexer, and Dashboard, along with the commands used to verify the services.
