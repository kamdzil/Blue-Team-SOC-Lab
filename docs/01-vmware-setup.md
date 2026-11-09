# Phase 1 — VMware Workstation Pro Setup

## Objective

Set up a virtual environment on a Windows computer to host an Ubuntu Server running Wazuh SIEM for security monitoring, threat detection, and incident investigation.

## Step 1 — Download VMware Workstation Pro

1. Visit the official Broadcom website.
2. Download VMware Workstation Pro for Windows.
3. Run the installer and complete the installation.
4. Launch VMware Workstation Pro.

## Step 2 — Download Ubuntu Server

1. Visit https://ubuntu.com/download/server
2. Download the Ubuntu Server ISO compatible with the virtual machine.
3. Save the ISO file locally.

## Step 3 — Create the Virtual Machine

1. Open VMware Workstation Pro.
2. Select **Create a New Virtual Machine**.
3. Choose the Ubuntu Server installation ISO.
4. Select Linux as the guest operating system and the appropriate Ubuntu version.
5. Name the virtual machine `SOC-Wazuh-Server`.
6. Configure the virtual hardware.

### Virtual Machine Configuration

| Component | Configuration |
|---|---|
| Host operating system | Windows |
| Virtualization software | VMware Workstation Pro |
| Guest operating system | Ubuntu Server |
| Virtual disk | 50 GB |
| Network adapter | NAT |
| CPU | To be confirmed |
| RAM | To be confirmed |

## Step 4 — Configure Networking

1. Open the virtual machine settings.
2. Select **Network Adapter**.
3. Choose **NAT (Network Address Translation)**.
4. Save the settings.

NAT networking allows the Ubuntu virtual machine to communicate with the Windows host and access external networks through the host's connection.

### Network Configuration

The virtual machine receives a private IP address through VMware's virtual network.

Example IP address:

`192.168.xxx.xxx`

**Note:** The IP address shown above is a placeholder. Actual IP addresses vary depending on VMware's network configuration.

## Step 5 — Start the Virtual Machine

1. Power on the Ubuntu virtual machine.
2. Boot from the Ubuntu Server ISO.
3. Begin the Ubuntu Server installation.
4. Follow the installation wizard to configure the operating system.

The Ubuntu installation process will be documented separately in `02-ubuntu-setup.md`.

## Step 6 — Verify the Virtual Machine

After Ubuntu Server is installed, open the terminal and identify the assigned IP address.

```bash
hostname -I
```

View the network routing configuration:

```bash
ip route
```

From the Windows host, verify connectivity using Command Prompt:

```cmd
ping <VM_IP_ADDRESS>
```

Replace `<VM_IP_ADDRESS>` with the actual IP address assigned to the Ubuntu virtual machine.

A successful ping confirms that the Windows host can communicate with the Ubuntu VM.

## Result

Successfully configured an Ubuntu Server virtual machine using VMware Workstation Pro.

The environment provides the foundation for deploying Wazuh SIEM and building a Blue Team Security Operations Center (SOC) home lab.

## Screenshots

The following screenshots will be added to document the setup:

- VMware Workstation Pro installation
- Virtual machine hardware configuration
- Virtual disk configuration (50 GB)
- NAT network configuration
- Ubuntu Server running inside VMware
- Successful network connectivity test

## Next Phase

**Phase 2 — Ubuntu Server Configuration**

Document the Ubuntu installation process, system updates, Linux commands, and initial server configuration.
