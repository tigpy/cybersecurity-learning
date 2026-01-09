# Practical 2 – AAA Configuration using Local, TACACS+, and RADIUS

## Objective
To implement Authentication, Authorization, and Accounting (AAA) using local authentication, TACACS+, and RADIUS on Cisco routers.

## Technologies Used
- Cisco Packet Tracer
- AAA (Authentication, Authorization, Accounting)
- TACACS+
- RADIUS
- SSH
- OSPF

## Practical Breakdown

### Part 1 – OSPF
OSPF was configured to ensure connectivity between routers and AAA servers.

### Part 2 – Local AAA (R1)
Local user authentication was configured on R1.
This acts as a fallback mechanism in case centralized servers fail.

### Part 3 – TACACS+ Authentication (R2)
TACACS+ was implemented to provide centralized authentication for router administration.
A TACACS+ server was configured, and R2 was registered as a client.

### Part 4 – RADIUS Authentication (R3)
RADIUS was configured to authenticate users centrally.
R3 was integrated with the RADIUS server for SSH login authentication.

## Verification
- SSH login using TACACS+ credentials on R2
- SSH login using RADIUS credentials on R3
- Server reachability and authentication validation

## Security Relevance
AAA is a core enterprise security control that enforces identity-based access.
TACACS+ is used for administrative access, while RADIUS is widely used for user authentication.

## Conclusion
This practical demonstrates real-world enterprise authentication mechanisms and highlights the importance of centralized access control in secure networks.
