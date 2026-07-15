# Notes - It's Oops PM

> Quick revision notes for FPGA, VHDL, Digital Logic, and Hardware Backdoors.

---

# Challenge Summary

- **Category:** Hardware
- **Difficulty:** Very Easy
- **Platform:** Hack The Box - CTF Try Out

---

# Important Concepts

## VHDL

VHDL (VHSIC Hardware Description Language) is a Hardware Description Language used to describe digital circuits.

Unlike C or Python, VHDL does **not** describe instructions executed by a CPU.

Instead, it describes how electronic components are connected together.

Example:

```
Input A
      │
      ▼
    XOR Gate
      │
      ▼
Output
```

---

## FPGA

FPGA = **Field Programmable Gate Array**

Characteristics:

- Reprogrammable hardware
- Parallel execution
- Used in embedded systems
- Common in aerospace, automotive, military, and industrial control systems

---

## Digital Logic Levels

```
LOW  = 0

HIGH = 1
```

Digital circuits only understand binary signals.

---

## Common Logic Gates

### AND

```
1 AND 1 = 1

Otherwise = 0
```

---

### OR

```
1 OR 0 = 1

0 OR 0 = 0
```

---

### NOT

```
NOT 1 = 0

NOT 0 = 1
```

---

### XOR

Outputs HIGH only if inputs are different.

Truth Table

| A | B | Output |
|---|---|--------|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

Uses:

- Encryption
- Error detection
- Digital arithmetic

---

# Multiplexer (MUX)

Purpose:

Selects one input from multiple inputs.

```
Input A
      │
      ▼
   ┌─────┐
──►│MUX  │──► Output
   └─────┘
      ▲
      │
Input B
```

---

# Hardware Backdoor

A hidden hardware function that activates only under specific conditions.

Examples:

- Secret binary input
- Hidden register
- Debug mode
- Special timing sequence

Unlike software backdoors, hardware backdoors exist inside the circuit itself.

---

# Binary Signals

The challenge required a **16-bit binary input**.

Example:

```
1010101010101010
```

Only the correct input activates the hidden functionality.

---

# Netcat

Basic syntax:

```bash
nc <IP> <PORT>
```

Example:

```bash
nc 154.xxx.xxx.xxx 31715
```

---

# Investigation Checklist

When analyzing VHDL:

- Identify entity inputs
- Identify outputs
- Understand architecture
- Follow signal assignments
- Locate conditional statements
- Search for hidden logic
- Identify magic constants
- Trace output path

---

# Solving Methodology

```
Read Challenge
      │
      ▼
Understand VHDL
      │
      ▼
Analyze Logic
      │
      ▼
Find Hidden Condition
      │
      ▼
Generate Correct Input
      │
      ▼
Trigger Backdoor
```

---

# Key Lessons

- Never guess binary inputs.
- Understand the circuit first.
- Hardware behaves differently from software.
- VHDL represents real electronic logic.
- Backdoors may exist only under rare input conditions.

---

# Real-World Applications

Knowledge from this challenge applies to:

- FPGA Security
- Hardware Trojan Detection
- Embedded Security
- Secure Hardware Design
- Military Electronics
- Supply Chain Security

---

# Interview Questions

### What is VHDL?

A hardware description language used to model and design digital circuits.

---

### Difference between FPGA and CPU?

CPU executes software instructions sequentially.

FPGA implements hardware logic that runs in parallel.

---

### What is a hardware backdoor?

A hidden hardware function intentionally added to a digital design that activates only under specific conditions.

---

### What is XOR commonly used for?

- Encryption
- Error detection
- Digital arithmetic

---

### What is a Multiplexer?

A digital component that selects one input from multiple inputs.

---

# Commands Used

```bash
nc <IP> <PORT>
```

---

# Revision Checklist

- [ ] Understand VHDL
- [ ] Understand FPGA
- [ ] Understand XOR
- [ ] Understand Multiplexer
- [ ] Understand Binary Signals
- [ ] Understand Hardware Backdoors
- [ ] Understand Netcat Interaction

---

# Next Topics

After this challenge:

- PCB Reverse Engineering
- Gerber Files
- UART
- Saleae Logic Analyzer
- SPI
- I²C
- JTAG
- SWD

---

# Personal Notes

Things I learned from this challenge:

- Reading hardware logic is similar to reading source code but represents circuits instead of software.
- Hardware backdoors can be hidden inside FPGA logic.
- A systematic analysis is more effective than guessing inputs.
- Understanding digital logic is the foundation of hardware security.
