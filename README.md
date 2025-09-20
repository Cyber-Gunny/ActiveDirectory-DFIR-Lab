# Active Directory DFIR Lab

This project is a home lab built to practice **Active Directory (AD) administration, SOC analysis, and detection engineering**.  
The lab integrates **Splunk, Sysmon, and Atomic Red Team** to simulate attacks and analyze detection coverage.

---

## 🖥️ Lab Overview
- **Domain**: MyDFIR  
- **Network**: 192.168.10.0/24  
- **Hosts**:
  - Splunk Server: `192.168.10.10`
  - Active Directory Domain Controller: `192.168.10.7`
  - Windows 11 Workstation: DHCP
  - Kali Linux Attacker: `192.168.10.250`

---

## 🗂️ Lab Diagram
*(Diagram coming soon — will be uploaded under `/Diagrams`)*  
![Network Diagram](Diagrams/network_diagram.png)

---

## 🔧 Tools & Services
- **Splunk Enterprise** – SIEM for log analysis  
- **Splunk Universal Forwarder** – endpoint log forwarding  
- **Sysmon** – Windows telemetry for process/registry monitoring  
- **Atomic Red Team** – adversary simulation framework  
- **Kali Linux** – attacker system  

---

## 🎯 Project Goals
1. Stand up an Active Directory domain.  
2. Forward endpoint logs to Splunk using Sysmon + Universal Forwarder.  
3. Simulate attacks with Atomic Red Team.  
4. Detect and visualize activity in Splunk dashboards.  
5. Build detection rules mapped to MITRE ATT&CK.  

---

## 📂 Repository Structure

