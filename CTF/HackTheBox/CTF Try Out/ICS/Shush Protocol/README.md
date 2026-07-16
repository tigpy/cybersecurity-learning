# Shush Protocol

**Category:** ICS  
**Difficulty:** Very Easy  
**Platform:** Hack The Box - CTF Try Out

---

## Scenario

The crew sets their sights on an abandoned fertilizer plant, searching for a cache of ammonium nitrate hidden within its aging infrastructure. Inside the control room, they discover a legacy PLC that is still operational.

To gain complete access, they need the password used by a diagnostic control device communicating with the PLC. After capturing the network traffic between the industrial computer and the PLC, the objective is to recover the hidden secret.

---

## Objective

Analyze the provided network capture and recover the flag.

---

## Files Provided

```
traffic.pcapng
```

---

# Initial Analysis

Since the challenge revolves around Industrial Control Systems (ICS), the first step was to inspect the packet capture using **Wireshark**.

## Protocol Hierarchy

Navigate to:

```
Statistics → Protocol Hierarchy
```

The capture revealed only one industrial protocol.

| Protocol | Details |
|----------|---------|
| Modbus/TCP | Industrial communication protocol |
| TCP | Port 502 |
| IPv4 | Internal network |

---

## Network Communication

The capture showed communication between:

| Source | Destination |
|---------|-------------|
| 192.168.178.105 | 192.168.178.23 |

Port:

```
502/TCP
```

which indicates **Modbus/TCP**.

---

# Modbus Analysis

Applying the filter:

```text
modbus
```

revealed multiple requests and responses.

The client repeatedly queried the PLC using:

```
Function Code 03
Read Holding Registers
```

Example:

```
Reference Number : 10
Word Count       : 100
```

The PLC responded successfully.

---

## Holding Registers

Examining the responses showed:

```
Register 10 : 0
Register 11 : 0
Register 12 : 0
...
Register 109 : 0
```

Every holding register contained:

```
0
```

No credentials or hidden ASCII data were present.

---

## Coil Analysis

Additional traffic used:

```
Function Code 01
Read Coils
```

The returned coil values were also entirely zero.

---

## Vendor Specific Function

Some packets contained:

```
Function Code 102 (0x66)
```

which is a vendor-specific Modbus function.

However, inspection of these packets also revealed no password or flag.

---

# Changing the Approach

Since the Modbus traffic contained no useful information, the next step was to perform basic forensic triage on the provided PCAP file.

Instead of focusing only on packet contents, printable strings inside the capture file were extracted.

```bash
strings traffic.pcapng
```

Searching specifically for the HTB flag format:

```bash
strings traffic.pcapng | grep -i "HTB"
```

returned:

```text
HTB{50m371m35_cu570m_p2070c01_423_n07_3n0u9h7}
```

---

# Flag

```text
HTB{50m371m35_cu570m_p2070c01_423_n07_3n0u9h7}
```

---

# Why It Works

A **PCAPNG** file contains more than captured packets.

Besides packet payloads, it can also include:

- Interface Description Blocks
- Name Resolution Blocks
- Capture Comments
- Custom Option Blocks
- Metadata

The challenge author embedded the flag as printable text inside the PCAPNG file rather than transmitting it over the Modbus protocol.

Because of this, the Unix `strings` utility successfully extracted the hidden flag.

---

# Tools Used

- Wireshark
- strings
- grep

---

# Commands Used

```bash
strings traffic.pcapng

strings traffic.pcapng | grep -i "HTB"
```

---

# Lessons Learned

- Never assume the flag is transmitted over the observed protocol.
- Always perform initial file triage before deep protocol analysis.
- PCAPNG files may contain useful metadata beyond packet payloads.
- Simple utilities like `strings` can quickly reveal embedded secrets.
- In CTFs, thinking beyond protocol analysis can save significant time.

---

# MITRE ATT&CK for ICS Mapping

| Technique | Description |
|----------|-------------|
| T0842 | Network Sniffing |
| T0888 | Point & Tag Identification |
| T0801 | Monitor Process State |

---

# Skills Practiced

- Wireshark packet analysis
- Modbus protocol inspection
- Industrial network reconnaissance
- PCAP forensic analysis
- Linux command-line investigation

---


