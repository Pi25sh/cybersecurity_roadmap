# 🧪 Free & Budget Home Lab Architecture Guide

Building your own home lab is one of the highest-leverage investments you can make in your cybersecurity journey. It gives you unrestricted permission to break, inspect, and defend operating systems in an isolated environment.

---

## 🏗️ Recommended Lab Topologies

### Tier 1: Single-Host Laptop / Desktop Setup (Budget / Zero Cost)
- **Host Specs**: 16 GB RAM, 4+ CPU cores, 100+ GB free SSD storage.
- **Hypervisor**: [VirtualBox](https://www.virtualbox.org/) (FOSS) or [VMware Workstation Pro](https://www.vmware.com/products/desktop-hypervisors/workstation-and-fusion) (Free for personal use).
- **Virtual Machines**:
  1. **Attacker VM**: Kali Linux or Parrot Security OS (2 vCPU, 4 GB RAM).
  2. **Domain Controller**: Windows Server 2022 Evaluation (2 vCPU, 4 GB RAM) — 180-day free trial.
  3. **Domain Member Workstation**: Windows 10/11 Enterprise Evaluation (2 vCPU, 4 GB RAM) — 90-day free trial.
  4. **SIEM / Logging Host**: Ubuntu Server running Wazuh All-in-One (2 vCPU, 4 GB RAM).

### Tier 2: Dedicated Mini-PC / Server Setup (Intermediate)
- **Hardware**: Used enterprise mini-PC (e.g., HP EliteDesk, Lenovo ThinkCentre, Dell OptiPlex) with 32–64 GB RAM.
- **Hypervisor**: [Proxmox VE](https://www.proxmox.com/en/proxmox-virtual-environment/overview) (Free, open-source Type-1 hypervisor).
- **Virtual Router / Firewall**: pfSense or OPNsense VM isolating the attack subnet from your home LAN.

---

## 🚀 Step-by-Step Setup Guide

### Step 1: Network Isolation
Never bridge your lab VMs directly to your home Wi-Fi/LAN when detonating malware or testing exploits.
- In VirtualBox: Set the network adapter on all lab VMs to an **Internal Network** (e.g., `LabNet`) or a **NAT Network**.
- Assign static or DHCP IP addressing within an internal subnet like `192.168.56.0/24`.

### Step 2: Provisioning Active Directory
1. Download the [Windows Server Evaluation ISO](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022).
2. Install Windows Server and install the **Active Directory Domain Services (AD DS)** role.
3. Promote the server to a Domain Controller for a test domain (e.g., `lab.local`).
4. Create sample Organizational Units (OUs), users (e.g., `svc-backup`, `jdoe`), and service accounts with Service Principal Names (SPNs) for Kerberoasting practice:
   ```powershell
   setspn -a MSSQLSvc/sqlserver.lab.local:1433 svc-sql
   ```

### Step 3: Installing Telemetry Sensors (Sysmon)
1. Download [Microsoft Sysinternals Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon).
2. Download a community-vetted modular configuration, such as [SwiftOnSecurity Sysmon-Config](https://github.com/SwiftOnSecurity/sysmon-config).
3. Install Sysmon on the Windows workstation:
   ```cmd
   sysmon64.exe -i sysmonconfig-export.xml
   ```
4. Verify Sysmon events are logging in `Applications and Services Logs -> Microsoft -> Windows -> Sysmon -> Operational`.

### Step 4: Installing the Wazuh SIEM & EDR
1. Spin up an Ubuntu 22.04 LTS VM.
2. Run the Wazuh Quickstart installation script:
   ```bash
   curl -sO https://packages.wazuh.com/4.8/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
   ```
3. Access the web dashboard via `https://<Ubuntu_IP>`.
4. Deploy the Wazuh agent onto your Windows Server and Windows 11 clients to immediately stream logs and Sysmon telemetry.

---

## ⚡ Automated Lab Provisioning Alternatives
If you prefer not to build the Active Directory environment manually:
- **[DetectionLab](https://github.com/clong/DetectionLab)**: Automates the creation of an Active Directory domain with Sysmon, Splunk, Zeek, and Fleet/Osquery via Vagrant or Terraform.
- **[GOAD (Game of Active Directory)](https://github.com/Orange-Cyberdefense/GOAD)**: A vulnerable Active Directory lab environment designed for offensive and defensive practice with multi-domain forest setups.
