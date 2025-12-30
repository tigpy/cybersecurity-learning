# 🌐 Networking Notes for Ethical Hackers  
## 7 Core Concepts (Recon → Exploitation → Defense)

---

## 1️⃣ IP Addressing & Subnetting (Recon Foundation)

### IP Address
- Identity of a device on a network  
- Destination where packets are delivered  

### IPv4
- 32-bit (e.g. `192.168.1.1`)
- Limited address space
- Most attacks still target IPv4

### IPv6
- 128-bit
- Massive address space
- Changes scanning and recon strategy

### Public vs Private IP
- **Private IP** → Internal network mapping  
- **Public IP** → External attack surface  

If a public IP exposes a service, it is **attack surface**, not noise.

---

### Subnetting (Attacker’s Map)
Subnetting = dividing a network into logical parts.

Example:
192.168.1.0/24
Network ID: 192.168.1.0
Broadcast: 192.168.1.255
Usable Hosts: 254


CIDR decides:
- How many hosts exist
- Scan scope
- Bug bounty scope

**Hacker view:**  
IP + Subnet = target map, not math.

---

## 2️⃣ MAC Address, ARP & MITM

### MAC Address
- Hardware identity (Layer 2)
- Format: `AA:BB:CC:DD:EE:FF`
- First half = manufacturer (OUI)

### MAC Spoofing
- Change MAC to impersonate trusted device
- Breaks MAC filtering
- Common in Wi-Fi attacks

---

### ARP (Trust Problem)
ARP maps:
IP → MAC

Problem:
- No verification
- First reply is trusted

---

### ARP Spoofing (MITM)
Attacker claims:
- “I am the router”
- “I am the victim”

Result:
- All traffic passes through attacker
- Credentials, sessions, DNS exposed
- Packets can be modified live

---

### Defense
- Static ARP (critical systems)
- Dynamic ARP Inspection
- Packet monitoring
- HTTPS, VPN, SSH everywhere

---

## 3️⃣ DNS & Domain Spoofing

### DNS
Maps:
Domain → IP

### Resolution Flow
Client → Resolver → Root → TLD → Authoritative → Cache → Client

---

### DNS Attacks
- DNS Spoofing → fake reply first
- Cache Poisoning → corrupt resolver cache
- Typosquatting → look-alike domains

Why dangerous:
- URL looks legit
- User trusts browser
- Phishing becomes invisible

If DNS is compromised, **encryption alone is not enough**.

---

## 4️⃣ Ports & Protocols (Attack Surface)

### IP vs Port
- IP = where
- Port = what service  

Every open port = potential door.

### Critical Ports
- 21 → FTP (anonymous access)
- 22 → SSH (bruteforce)
- 80 / 443 → Web (SQLi, XSS, RCE)
- 445 → SMB (EternalBlue)
- 3389 → RDP (credential attacks)

---

### TCP vs UDP (Attacker View)
- **TCP** → reliable, logged, stateful  
- **UDP** → fast, silent, harder to detect  

UDP scans often reveal services firewalls miss.

---

## 5️⃣ OSI & TCP/IP (Attack Mapping)

### OSI as Attack Layers
- L1 → Physical tapping
- L2 → ARP spoofing, MAC flooding
- L3 → IP spoofing
- L4 → SYN flood
- L7 → SQLi, XSS, auth bypass

Hackers don’t memorize OSI.  
They map weakness by layer.

---

### TCP/IP
- OSI → learning model
- TCP/IP → real traffic model
- Every packet capture fits here

---

## 6️⃣ Routing, Switching & VLAN Security

### Switching
- Uses MAC table
- Vulnerable to MAC flooding

### Routing
- Uses routing table
- Misconfig can hijack traffic

---

### VLANs
- Logical separation
- Limits blast radius

### VLAN Hopping
- Misconfigured trunk ports
- Attacker jumps VLANs
- Reaches restricted zones

Segmentation works only if configured correctly.

---

## 7️⃣ Firewalls, NAT & Bypass Reality

### Firewall
- Rule-based packet filtering
- Not intelligent

### NAT
- Hides internal IPs
- Translation, not security

---

### Bypass Techniques
- Allowed ports (80, 443)
- DNS tunneling
- Payload hiding in HTTP
- Outbound-only C2

Firewalls block patterns, not intent.

---

## 🧠 Final Mental Model (Memorize This)
- IPs define scope
- Subnets define targets
- ARP breaks trust
- DNS breaks identity
- Ports expose doors
- OSI shows where to attack
- VLANs limit damage
- Firewalls filter, not protect

---

## 🔬 Practical Skills This Enables
- Subnetting in your head
- ARP MITM labs
- DNS spoofing demos
- Mapping Nmap output to OSI
- Reading Wireshark like an analyst
