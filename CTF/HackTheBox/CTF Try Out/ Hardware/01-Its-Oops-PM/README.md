# It's Oops PM

> **Category:** Hardware  
> **Platform:** Hack The Box – CTF Try Out  
> **Difficulty:** Very Easy  
> **Primary Concepts:** VHDL, FPGA, Digital Logic, Hardware Backdoors, XOR Logic

---

# Overview

This challenge introduces the fundamentals of hardware reverse engineering by requiring analysis of a VHDL design. Instead of exploiting software vulnerabilities, the objective is to understand how digital logic is implemented in hardware and identify a hidden condition that activates a backdoor.

Unlike traditional binary reverse engineering, VHDL describes **hardware behavior**, meaning the code represents logic gates, multiplexers, registers, and signal paths rather than CPU instructions.

This challenge serves as an excellent introduction to FPGA security and hardware design analysis.

---

# Learning Objectives

After completing this challenge, I was able to:

- Understand the basics of VHDL.
- Learn how digital circuits are described using HDL.
- Read and analyze hardware logic.
- Identify hidden logic conditions.
- Understand hardware backdoors.
- Communicate with a remote embedded service using Netcat.
- Trace digital signals through a logic design.

---

# Skills Covered

- Hardware Reverse Engineering
- FPGA Fundamentals
- VHDL Analysis
- Digital Logic
- XOR Operations
- Multiplexer (MUX)
- Binary Signal Analysis
- Network Interaction
- Hardware Backdoor Discovery

---

# Files Provided

The challenge provided:

- VHDL source files
- Hardware description
- Remote service accessible through Netcat

---

# Background Concepts

## What is VHDL?

VHDL (VHSIC Hardware Description Language) is a programming language used to describe electronic circuits.

Unlike Python or C, VHDL describes **hardware**, not software.

Instead of saying:

```text
Execute instruction A
```

it describes:

```text
Connect Signal A
to Logic Gate B
whose output goes into Register C
```

The result is an actual digital circuit after synthesis.

---

## FPGA

FPGA stands for:

> **Field Programmable Gate Array**

An FPGA is an integrated circuit that can be programmed after manufacturing.

Instead of writing software that runs on a CPU, you create the CPU's digital logic itself.

Applications include:

- Aerospace
- Automotive
- Industrial Control Systems
- Cryptography
- Telecommunications
- Artificial Intelligence
- Embedded Systems

---

## Digital Logic

Digital electronics operate using binary values.

```
LOW  = 0

HIGH = 1
```

Everything inside an FPGA is constructed using logic gates.

Common gates include:

- AND
- OR
- XOR
- NOT
- NAND
- NOR

---

## XOR Gate

The Exclusive OR (XOR) gate outputs HIGH only when the inputs are different.

| A | B | Output |
|---|---|--------|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

XOR is commonly used for:

- Encryption
- Error detection
- Digital arithmetic

---

## Multiplexer (MUX)

A multiplexer selects one input from several possible inputs.

Example:

```
      Input A
         │
         │
         ▼
       ┌─────┐
Select │ MUX │ Output
------►│     │──────►
       └─────┘
         ▲
         │
      Input B
```

Depending on the select signal, either Input A or Input B reaches the output.

---

# Understanding Hardware Backdoors

Unlike software backdoors, hardware backdoors are implemented directly inside the logic.

These hidden conditions may activate only when a very specific input pattern is received.

Possible triggers include:

- Magic binary values
- Specific timing
- Hidden registers
- Secret logic paths

Since they are implemented in hardware, traditional antivirus software cannot detect them.

---

# Challenge Methodology

Rather than guessing inputs, the investigation followed a structured process.

## Step 1

Read the VHDL source code.

Understand:

- Inputs
- Outputs
- Internal signals

---

## Step 2

Identify how signals propagate through the design.

Questions to ask:

- Which conditions activate outputs?
- Are there hidden branches?
- Are magic constants used?

---

## Step 3

Trace the control logic.

The objective is to determine whether any condition activates hidden functionality.

---

## Step 4

Construct the required binary input.

The challenge expects a **16-bit binary signal**.

Supplying the correct signal activates the hidden logic implemented inside the FPGA.

---

## Step 5

Interact with the remote service using Netcat.

```
nc <IP> <PORT>
```

Provide the binary signal when prompted.

If the trigger condition is satisfied, the service reveals the challenge flag.

---

# Investigation Workflow

```
Read Challenge

        │

        ▼

Understand VHDL

        │

        ▼

Analyze Logic Gates

        │

        ▼

Identify Hidden Condition

        │

        ▼

Generate Binary Input

        │

        ▼

Connect using Netcat

        │

        ▼

Trigger Backdoor
```

---

# Key Takeaways

- Hardware description languages describe circuits rather than software.
- FPGA designs can contain intentionally hidden functionality.
- Reading HDL is similar to reading source code but requires understanding digital logic.
- Structured analysis is significantly more effective than guessing inputs.

---

# Real-World Relevance

Hardware backdoors are a serious concern in modern cybersecurity.

Potential attack scenarios include:

- Malicious FPGA firmware
- Supply chain attacks
- Counterfeit hardware
- Unauthorized debug functionality
- Military hardware implants
- Embedded device tampering

Understanding HDL is valuable for:

- Hardware Security Engineers
- Embedded Security Researchers
- FPGA Developers
- Reverse Engineers
- Red Team Operators

---

# Tools Used

- Visual Studio Code
- Netcat
- Linux Terminal

---

# Lessons Learned

This challenge demonstrated that hardware security requires a different mindset than software security.

Instead of reversing machine code, the objective is to understand signal flow and circuit behavior.

Learning to read VHDL opens the door to FPGA reverse engineering, hardware trojan detection, and embedded system analysis.

---

# References

## FPGA

https://nandland.com/

## VHDL

https://vhdlwhiz.com/

## Digital Logic

https://www.allaboutcircuits.com/

## XOR Gates

https://en.wikipedia.org/wiki/XOR_gate

## Multiplexer

https://en.wikipedia.org/wiki/Multiplexer

---

# Next Topics

After mastering this challenge, the next concepts to study are:

- PCB Analysis
- Gerber Files
- UART
- Logic Analyzer
- SPI
- I²C
- JTAG
- SWD
- Firmware Reverse Engineering

---

> **Note:** This repository is intended for educational purposes. The focus is on understanding the underlying hardware security concepts and methodology rather than simply obtaining challenge flags.
