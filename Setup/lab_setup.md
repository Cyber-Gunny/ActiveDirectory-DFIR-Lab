# Lab Setup – Active Directory DFIR Lab

This document provides step-by-step instructions for building the Active Directory DFIR lab environment.  
The lab simulates a small enterprise network with a domain controller, a workstation, an attacker machine, and a Splunk SIEM for log collection and detection engineering.

---
## 📑 Table of Contents
1. [Environment Requirements](#-environment-requirements)
2. [Network Setup](#-network-setup)
3. [Active Directory Domain Controller](#-active-directory-domain-controller)
4. [Splunk Server](#-splunk-server)
5. [Windows 11 Workstation](#-windows-11-workstation)
6. [Kali Linux Attacker](#-kali-linux-attacker)
7. [Sysmon Setup](#-sysmon-setup)
8. [Atomic Red Team](#-atomic-red-team)
9. [Validation Checklist](#-validation-checklist)

## 1️⃣ Environment Requirements
- **Host Machine**
  - 16–32GB RAM
  - 250GB+ storage
  - Virtualization enabled (Intel VT-x / AMD-V)
- **Software**
  - VMware Workstation / VirtualBox
  - Windows Server 2022 ISO
  - Windows 11 ISO
  - Kali Linux ISO
  - Splunk Enterprise + Splunk Universal Forwarder
  - Sysmon (Windows Sysinternals)
  - Atomic Red Team

---

## 2️⃣ Network Setup
- **Subnet**: 192.168.10.0/24  
- **Planned Hosts**:  
  - Active Directory DC → 192.168.10.7  
  - Splunk Server → 192.168.10.10  
  - Windows 11 Client → DHCP  
  - Kali Attacker → 192.168.10.250  

---

## 3️⃣ Active Directory Domain Controller
1. Install **Windows Server 2022** in VM.  
2. Set static IP: `192.168.10.7`.  
3. Rename host to `DC01`.  
4. Install **Active Directory Domain Services** role.  
5. Promote to domain controller → create forest `MyDFIR.local`.  
6. Verify AD DS is running.  

---

## 4️⃣ Splunk Server
1. Install **Windows Server** or **Ubuntu Server**.  
2. Assign static IP: `192.168.10.10`.  
3. Install **Splunk Enterprise**.  
4. Login to Splunk Web → `http://192.168.10.10:8000`.  
5. Enable data input on port `9997` for forwarders.  

---

## 5️⃣ Windows 11 Workstation
1. Install Windows 11 Pro.  
2. Join domain: `MyDFIR.local`.  
3. Verify with `whoami` → `MYDFIR\username`.  
4. Install:
   - Splunk Universal Forwarder (send logs → Splunk Server)  
   - Sysmon (SwiftOnSecurity config)  
   - Atomic Red Team  

---

## 6️⃣ Kali Linux Attacker
1. Install Kali Linux.  
2. Set static IP: `192.168.10.250`.  
3. Update system:  
   ```bash
   sudo apt update && sudo apt upgrade -y
   ### 🛠 Tools Installed on Kali
- **nmap** – network scanning and enumeration  
- **impacket** – Python toolkit for working with network protocols (useful for Kerberos attacks and lateral movement)  
- **BloodHound** – Active Directory attack path analysis  
- **Metasploit Framework** – penetration testing and exploitation framework  

These tools will be used to simulate attacker behavior against the AD environment, and detections will be validated in Splunk.

---

## 7️⃣ Sysmon Setup
Sysmon (System Monitor) extends Windows logging by providing detailed event telemetry on process creation, network connections, registry changes, and more. This helps detect attacker techniques in real time.

1. Download Sysmon from [Microsoft Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon).  
2. Obtain the **SwiftOnSecurity Sysmon config** (recommended for broad coverage):  
   ```powershell
   git clone https://github.com/SwiftOnSecurity/sysmon-config.git
3. Install Sysmon with config:  
   ```powershell
   sysmon.exe -accepteula -i sysmonconfig.xml
4. Verify Sysmon is running:  
   - Open **Event Viewer** → Applications and Services Logs → Microsoft → Windows → Sysmon/Operational  
   - Confirm Event ID 1 (Process Creation) logs are being generated.  
5. Forward Sysmon logs to **Splunk Universal Forwarder**:  
   - Ensure the Splunk UF service is running.  
   - Confirm logs are received in Splunk (`index=main sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational`).  

✅ At this point, your domain controller and workstation are sending Sysmon telemetry into Splunk for monitoring and detection.

---

## 8️⃣ Atomic Red Team
Atomic Red Team (ART) is a library of small, portable tests that simulate attacker techniques from the MITRE ATT&CK framework.

1. Clone repo:  
   ```powershell
   git clone https://github.com/redcanaryco/atomic-red-team.git
2. Install Invoke-AtomicRedTeam PowerShell module.  
   ```powershell
   IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1')
   Install-AtomicRedTeam -GetPrereqs
3. Run a sample test (example: Credential Dumping - T1003):  
   ```powershell
   Invoke-AtomicTest T1003 -ShowDetails
   Invoke-AtomicTest T1003 -Confirm
4. Check that Sysmon generated logs and Splunk ingested them.  
   - In Splunk, search for:  
     ```
     index=main sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
     ```
   - Confirm events from the Atomic Red Team tests appear.  

---

## ✅ Validation Checklist
- [ ] Domain Controller functional  
- [ ] Windows 11 joined to domain  
- [ ] Splunk Web accessible at `http://192.168.10.10:8000`  
- [ ] Logs received from DC & workstation  
- [ ] Sysmon telemetry visible in Splunk  
- [ ] Atomic Red Team test events logged in Splunk  





