# WAZUH-Home-Lab--SIEM-and-File-Integrity-Monitoring
This project demonstrates how to build a Wazuh setup for file integrity monitoring.

## Table of Contents

- Overview
- Lab Architecture
- Prerequisites
- Installing the Wazuh Manager (Ubuntu)
- Accessing the Wazuh Dashboard
- Installing the Wazuh Agent (Windows Host)
- Registering the Agent with the Manager
- File Integrity Monitoring on Windows
- Verifying the Setup

## 1. Overview

Wazuh is a free, open-source security platform that provides:
- Log analysis
- File integrity monitoring
- Intrusion detection
- Vulnerability detection
- Real-time alerting

## 2. Lab Architecture

| Component      | Host    | Role                                                 |
|----------------|---------|------------------------------------------------------|
| Wazuh Manager  | Ubuntu  | Collects, analyzes, and stores data from agents      |
| Wazuh Agent    | Windows | Sends logs and system events to the Wazuh manager    |

Network: Use a **Bridged Adapter** in VirtualBox so both VMs are on the same network.

## 3. Prerequisites

- VirtualBox
- Ubuntu Server 20.04+ in VirtualBox (bridged networking)
- Internet access on Ubuntu VM
- Administrative access on Windows host

## 4. Installing the Wazuh Manager (Ubuntu)

1. **Add Wazuh GPG Key:**
   curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg

2. **Download and Execute Wazuh Installation Script:**
   curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i

   - `-a` installs all components
   - `-i` runs in interactive mode

## 5. Accessing the Wazuh Dashboard

- **Find your Ubuntu VM’s IP:**
  ifconfig
  
- Open browser: `https://<ubuntu-vm-ip>`
  
- Accept the security warning (self-signed cert) and log in with credentials shown post-install.

## 6. Installing the Wazuh Agent (Windows Host)

1. Download latest Windows agent MSI from [Wazuh documentation](https://documentation.wazuh.com/current/installation-guide/installing-wazuh-agent/win.html).

2. Run the installer with default settings.

## 7. Registering the Agent with the Manager

### 7.1 Generate Agent Key on Ubuntu
sudo /var/ossec/bin/manage_agents

- Select `A` to add agent, set a name (e.g., WindowsHost), and assign IP if needed.
- After creation, select `E` to extract key. **Copy** the key.

### 7.2 Apply Key in Windows Agent

- Open **Wazuh Agent Manager** on Windows.
- Paste the copied key.
- Enter the Wazuh manager’s IP (Ubuntu VM).
- Save/apply, then **restart the agent**.

## 8. File Integrity Monitoring on Windows

1. Edit: `C:\Program Files (x86)\ossec-agent\ossec.conf`
2. Add inside `<directories>`:
<directories realtime="yes">C:\Users\YOUR_USER\Test</directories>

3. Save and restart the agent service.

## 9. Verifying the Setup

- Access the Wazuh Dashboard.
- Check that the agent is active (Agents section).
- Go to Integrity Monitoring. Try creating, modifying, or deleting files in the monitored folder and confirm alerts in the dashboard.
