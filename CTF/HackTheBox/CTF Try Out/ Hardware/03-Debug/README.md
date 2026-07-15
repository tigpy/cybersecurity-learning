# Debug

> **Category:** Hardware  
> **Platform:** Hack The Box – CTF Try Out  
> **Difficulty:** Easy  
> **Primary Concepts:** Embedded Systems, UART, Logic Analyzer, Saleae Logic, Serial Communication, Bootloader Analysis

---

# Overview

This challenge introduces one of the most fundamental skills in embedded hardware security: **analyzing serial communication using a Logic Analyzer**.

Unlike software reverse engineering, where binaries or source code are analyzed, this challenge provides a **Saleae Logic Analyzer capture (.sal)**. The objective is to decode electrical signals captured during a device's boot sequence and extract meaningful information from the serial communication.

The challenge simulates a real-world scenario where an analyst has physical access to a device and captures its communication during startup.

---

# Learning Objectives

After completing this challenge, I was able to:

- Understand UART communication.
- Learn how a Logic Analyzer works.
- Decode serial communication using Saleae Logic.
- Configure an Async Serial Analyzer.
- Identify the correct UART parameters.
- Read embedded boot logs.
- Extract useful information from serial output.

---

# Skills Covered

- Embedded Systems
- UART Communication
- Logic Analyzer Analysis
- Saleae Logic
- Serial Debugging
- Bootloader Analysis
- Hardware Reverse Engineering

---

# Files Provided

The challenge provided:

- Logic Analyzer Capture (.sal)

The `.sal` file contains raw digital signal transitions captured during the boot process.

---

# Background Concepts

## Embedded Systems

An embedded system is a dedicated computing device designed to perform a specific function.

Examples include:

- Routers
- Smart TVs
- Drones
- Medical Devices
- Automotive ECUs
- IoT Devices
- Industrial Controllers

Most embedded systems communicate using hardware interfaces.

---

# UART

UART stands for:

> Universal Asynchronous Receiver Transmitter

UART is one of the most common serial communication protocols.

It requires only three essential connections.

```
Device A                 Device B

 TX  -----------------> RX

 RX  <----------------- TX

 GND ------------------ GND
```

---

# UART Frame

Every transmitted byte follows a structure.

```
Idle

↓

Start Bit

↓

8 Data Bits

↓

Optional Parity

↓

Stop Bit
```

Most embedded devices use:

- 8 Data Bits
- No Parity
- 1 Stop Bit

Also known as **8N1**.

---

# Baud Rate

The baud rate determines how quickly bits are transmitted.

Common values include:

- 9600
- 19200
- 38400
- 57600
- 115200

Both communicating devices must use the same baud rate.

---

# Logic Analyzer

A Logic Analyzer captures digital voltage transitions.

Unlike an oscilloscope, it focuses on digital communication rather than analog waveforms.

It records:

- HIGH
- LOW
- Timing

Protocol analyzers convert these transitions into readable data.

---

# Saleae Logic

Saleae Logic is a professional Logic Analyzer software used to decode hardware communication.

Supported protocols include:

- UART
- SPI
- I²C
- CAN
- USB
- Manchester
- LIN

---

# Challenge Methodology

The challenge provided a Logic Analyzer capture rather than decoded data.

The investigation followed these steps.

---

## Step 1

Open the `.sal` file inside Saleae Logic.

Observe:

- Available channels
- Signal activity
- Timing

---

## Step 2

Identify which channel carries meaningful communication.

Only one channel contained active serial data.

---

## Step 3

Add an **Async Serial Analyzer**.

Configure:

- Input Channel
- Baud Rate
- Data Bits
- Stop Bits
- Parity

---

## Step 4

Determine the correct baud rate.

115200 baud produced valid ASCII output.

Incorrect baud rates resulted in unreadable data.

---

## Step 5

Read the decoded UART output.

The boot sequence revealed diagnostic messages that contained the information required to solve the challenge.

---

# Investigation Workflow

```
Receive .sal File

        │

        ▼

Open Saleae Logic

        │

        ▼

Inspect Channels

        │

        ▼

Add UART Analyzer

        │

        ▼

Configure Parameters

        │

        ▼

Decode ASCII

        │

        ▼

Read Boot Logs

        │

        ▼

Extract Useful Information
```

---

# Key Takeaways

- Logic analyzers capture electrical signals rather than text.
- Protocol analyzers translate digital transitions into meaningful communication.
- UART remains one of the most widely used debugging interfaces in embedded systems.
- Boot logs often reveal valuable diagnostic information.

---

# Real-World Relevance

UART analysis is commonly used in:

- Firmware Analysis
- IoT Security
- Router Hacking
- Embedded Device Assessment
- Hardware Debugging
- Bootloader Analysis
- Incident Response
- Reverse Engineering

Sensitive information exposed over UART may include:

- Firmware Versions
- Debug Messages
- Memory Addresses
- Credentials
- Configuration Information
- Boot Parameters

---

# Tools Used

- Saleae Logic 2
- Async Serial Analyzer
- UART Decoder

---

# Lessons Learned

Serial communication is often one of the easiest entry points into an embedded system.

Capturing and decoding UART traffic provides valuable insight into the internal operation of a device.

Understanding communication protocols is an essential skill for embedded security researchers.

---

# Common Mistakes

❌ Choosing the wrong baud rate.

❌ Decoding the wrong channel.

❌ Forgetting protocol analyzers.

❌ Assuming captured waveforms are already readable.

❌ Ignoring boot messages.

---

# Investigation Checklist

When analyzing a UART capture:

- Identify active channels.
- Check idle signal level.
- Add Async Serial Analyzer.
- Configure 8N1.
- Test common baud rates.
- Verify decoded ASCII.
- Read boot logs.
- Look for credentials, debug messages, or secrets.

---

# References

## Saleae Logic

https://support.saleae.com/

## UART

https://learn.sparkfun.com/tutorials/serial-communication

## Embedded Systems

https://www.embedded.com/

## ARM Trusted Firmware

https://trustedfirmware.org/

---

# Next Topics

After mastering UART, continue with:

- SPI
- I²C
- CAN Bus
- JTAG
- SWD
- OpenOCD
- Firmware Extraction
- EEPROM Analysis
- Secure Boot

---

# Summary

This challenge introduced embedded debugging through UART communication.

By configuring a Logic Analyzer correctly and decoding the captured serial traffic, it became possible to inspect the boot process of the embedded device and recover valuable information.

The challenge reinforces one of the most important principles of embedded security:

> **Raw electrical signals become valuable intelligence only after they are decoded using the correct protocol and parameters.**

---

> **Note:** This repository is intended for educational purposes. The focus is on understanding embedded communication protocols and hardware security methodologies rather than simply documenting challenge solutions.
