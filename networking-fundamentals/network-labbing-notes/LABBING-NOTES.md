# Labbing Notes – Cybersecurity & Networking

Concise notes for understanding labbing concepts before performing real practicals.

---

## What is Labbing?
Labbing means creating a controlled environment to practice:
- Attacks
- Defense
- Networking
- Troubleshooting  

Without touching real systems.

### Types of Labbing
- Home Labbing → Learning and practice
- Enterprise Labbing → Real organization simulation

---

## HOME LABBING

### Meaning
A home lab is a small, safe setup using:
- Your system
- Virtual machines
- Virtual networks

### Purpose
- Learn networking
- Practice hacking and defense
- Break systems safely

---

## Core Components of a Home Lab

### Host Machine
- Your real laptop / PC
- Runs VirtualBox or VMware
- Controls all virtual machines

### Virtual Machines (VMs)
Examples:
- Kali Linux → Attacker
- Windows → Victim
- Metasploitable → Vulnerable target

Each VM behaves like a real computer.

### Virtual Network
- Decides who can talk to whom
- Most important part of labbing

---

## Home Lab Network Modes

### NAT
- Internet access: Yes
- VM ↔ VM: No
- Use for updates and installs only

### Host-Only
- Internet: No
- Host ↔ VM: Yes
- VM ↔ VM: Yes
- Use for safe attack labs

### Internal Network
- Internet: No
- Host ↔ VM: No
- VM ↔ VM: Yes
- Use for Kali vs vulnerable VM labs

### Bridged
- Internet: Yes
- VM joins real Wi-Fi / LAN
- Use for MITM and ARP spoofing (own network only)

### NAT Network
- Internet: Yes
- VM ↔ VM: Yes
- Safer than Bridged for multi-VM labs

### Not Attached
- No network
- Use for malware analysis and isolation

---

## Common Home Lab Topologies

### Basic Attack Lab
Kali ↔ Metasploitable  
Network: Internal

### Internet + Attack Lab
Kali:
- Adapter 1: NAT (Internet)
- Adapter 2: Host-Only (Attack)

### MITM Lab
Kali ↔ Victim ↔ Router  
Network: Bridged

---

## Important Home Lab Terms
- IP Address → Identity of a machine
- Gateway → Router
- Interface → eth0 / wlan0
- Broadcast → Send to all devices
- Packet Forwarding → Passing traffic silently
- Sniffing → Capturing traffic
- Segmentation → Network separation

---

## ENTERPRISE / ORGANIZATION LABBING

### Meaning
Simulating how real company networks are built, attacked, and defended.

### Purpose
- Red team exercises
- Blue team defense
- Incident response
- Compliance testing

---

## Enterprise Network Structure
Internet  
↓  
Firewall  
↓  
DMZ  
↓  
Internal Network  
↓  
Secure Zones

---

## Enterprise Zones

### DMZ
- Public-facing servers
- Web, Mail, VPN
- Sacrificial zone

### Internal Network
- Employee systems
- HR, Finance, Development
- Heavily monitored

### Secure Zones
- Critical assets
- Databases, Domain Controllers
- Restricted access

---

## Enterprise Networking Concepts

### VLAN
- Logical network separation
- HR ≠ Finance ≠ Guest
- Limits lateral movement

### Segmentation
- Divide network into security zones
- Reduces attack impact

### Zero Trust
- Never trust, always verify
- Internal traffic is also checked

### Jump Server / Bastion Host
- Single controlled access point
- Logged and monitored

### East-West Traffic
- Internal system-to-system traffic

### North-South Traffic
- Organization ↔ Internet traffic

---

## Enterprise Attack Flow (High Level)
1. Initial access
2. Foothold
3. Privilege escalation
4. Lateral movement
5. Persistence
6. Data exfiltration

---

## Enterprise Defense Focus
- Network → Firewall, IDS
- Endpoint → EDR
- Logs → SIEM
- Identity → IAM
- Monitoring → SOC

---

## Home Lab vs Enterprise Lab

Home Lab:
- Small scale
- Low risk
- Learn how attacks work

Enterprise Lab:
- Large scale
- High risk
- Learn how attacks are detected and stopped

---

## One-Line Memory
Home lab → Learn how attacks work  
Enterprise lab → Learn how attacks are stopped
