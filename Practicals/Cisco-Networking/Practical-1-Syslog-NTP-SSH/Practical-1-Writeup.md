# Practical 1 – Cisco Router Configuration: Syslog, NTP, and SSH

## Objective
To configure Cisco routers with secure management services including Syslog, NTP, and SSH, and verify connectivity using OSPF routing.

## Technologies Used
- Cisco Packet Tracer
- OSPF (Open Shortest Path First)
- Syslog Server
- NTP Server
- SSH (Secure Shell)

## Topology Overview
The network consists of three routers interconnected using serial links and LAN segments connected via switches. Centralized servers are used for Syslog and NTP services.

## Configuration Summary

### 1. OSPF Routing
OSPF was configured to enable dynamic routing between all routers.
This ensures full network reachability and scalability.

### 2. Syslog Configuration
Routers were configured to send logs to a centralized Syslog server.
This helps in monitoring network events and security incidents.

### 3. NTP Configuration
An NTP server was configured to synchronize time across all routers.
Accurate timestamps are critical for log correlation and forensic analysis.

### 4. SSH Configuration
SSH was enabled to allow secure remote access to routers.
Telnet was disabled to prevent plaintext credential exposure.

## Verification
- Ping tests between PCs
- `show ip route`
- `show logging`
- `show ntp status`
- SSH login verification

## Security Relevance
This practical demonstrates foundational infrastructure security concepts such as centralized logging, time synchronization, and secure remote administration.

## Conclusion
The network was successfully secured using standard enterprise practices. These configurations form the backbone of secure network operations.
