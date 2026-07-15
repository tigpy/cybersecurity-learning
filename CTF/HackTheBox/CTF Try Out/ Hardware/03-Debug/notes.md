# Notes - Debug

> Quick revision notes for Embedded Systems, UART, Logic Analyzer, and Serial Communication.

---

# Challenge Summary

- **Category:** Hardware
- **Difficulty:** Easy
- **Platform:** Hack The Box – CTF Try Out

---

# Important Concepts

## Embedded Systems

An embedded system is a computer designed for a specific purpose rather than general computing.

Examples:

- Routers
- IoT Devices
- Smart TVs
- Drones
- Industrial Controllers
- Automotive ECUs
- Medical Equipment

Unlike desktops, embedded devices often communicate using hardware interfaces.

---

# UART (Universal Asynchronous Receiver Transmitter)

UART is one of the simplest and most common serial communication protocols.

It allows two devices to exchange data asynchronously.

Typical Connections

```
Device A                Device B

TX ------------------> RX

RX <------------------ TX

GND ------------------ GND
```

---

# UART Frame

Every transmitted byte follows a standard format.

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

Known as:

```
8N1
```

---

# Baud Rate

The baud rate defines how quickly bits are transmitted.

Common baud rates:

- 9600
- 19200
- 38400
- 57600
- 115200

Both devices must use the same baud rate.

Incorrect baud rate = unreadable data.

---

# Logic Analyzer

A Logic Analyzer captures digital signals.

Unlike an oscilloscope:

Oscilloscope

- Analog voltage

Logic Analyzer

- Digital HIGH/LOW
- Timing
- Protocol decoding

---

# Saleae Logic

Saleae Logic is software used to analyze captured digital signals.

Supported protocols include:

- UART
- SPI
- I²C
- CAN
- USB
- LIN

---

# Async Serial Analyzer

Purpose:

Convert electrical transitions into UART data.

Typical Configuration

```
Channel

↓

Baud Rate

↓

8 Data Bits

↓

No Parity

↓

1 Stop Bit

↓

ASCII Output
```

---

# UART Workflow

```
Electrical Signal

↓

Logic Analyzer

↓

UART Decoder

↓

Bytes

↓

ASCII

↓

Readable Text
```

---

# Bootloader

The bootloader is the first program executed when an embedded device powers on.

Typical boot logs include:

- Firmware Version
- Boot Status
- Hardware Initialization
- Errors
- Debug Messages

These logs may reveal useful security information.

---

# Typical UART Pins

```
TX

RX

GND

VCC (sometimes)
```

Never connect TX → TX.

Correct wiring:

```
TX → RX

RX → TX

GND → GND
```

---

# Investigation Checklist

When receiving a Logic Analyzer capture:

✅ Identify active channels

✅ Ignore inactive channels

✅ Add Async Serial Analyzer

✅ Configure UART

✅ Start with 115200 baud

✅ If unreadable:

- 57600
- 38400
- 19200
- 9600

✅ Read decoded ASCII

---

# Solving Methodology

```
Receive Capture

      │

      ▼

Open Saleae Logic

      │

      ▼

Inspect Channels

      │

      ▼

Identify Active Signal

      │

      ▼

Add UART Analyzer

      │

      ▼

Configure 8N1

      │

      ▼

Determine Baud Rate

      │

      ▼

Decode ASCII

      │

      ▼

Read Boot Messages
```

---

# Common UART Baud Rates

| Baud Rate | Usage |
|------------|-------|
|9600|Legacy Devices|
|19200|Industrial Devices|
|38400|Networking Equipment|
|57600|Embedded Devices|
|115200|Modern Embedded Systems|

---

# Common Mistakes

❌ Wrong baud rate

❌ Wrong channel

❌ Wrong signal polarity

❌ Ignoring idle level

❌ Forgetting to add protocol analyzer

---

# Real-World Applications

UART analysis is used for:

- Firmware Debugging
- Router Hacking
- IoT Security
- Hardware Forensics
- Embedded Reverse Engineering
- Device Recovery
- Bootloader Analysis

---

# Useful Software

- Saleae Logic
- PulseView
- Sigrok
- Logic 2

---

# Interview Questions

## What is UART?

UART is an asynchronous serial communication protocol used for communication between embedded devices.

---

## Why is UART important?

Because developers use it for debugging, logging, firmware updates, and diagnostics.

---

## What is a Logic Analyzer?

A Logic Analyzer captures digital electrical signals and decodes communication protocols.

---

## Difference between Logic Analyzer and Oscilloscope?

Logic Analyzer

- Digital signals
- Protocol decoding

Oscilloscope

- Analog waveforms
- Voltage analysis

---

## What is 8N1?

- 8 Data Bits
- No Parity
- 1 Stop Bit

Most common UART configuration.

---

## Why is baud rate important?

Both communicating devices must use the same baud rate.

Otherwise, decoded data becomes unreadable.

---

# Revision Checklist

- [ ] Understand UART
- [ ] Understand Serial Communication
- [ ] Understand Logic Analyzer
- [ ] Understand Saleae Logic
- [ ] Understand Bootloader
- [ ] Know UART Wiring
- [ ] Know 8N1
- [ ] Know Common Baud Rates
- [ ] Decode UART Captures

---

# Personal Notes

Things I learned:

- UART is one of the easiest ways to inspect embedded devices.
- Logic analyzers capture digital transitions, not text.
- Protocol analyzers convert transitions into meaningful communication.
- Boot logs reveal valuable information about embedded systems.
- Choosing the correct baud rate is essential for successful decoding.

---

# Next Topics

After UART, continue learning:

- SPI
- I²C
- CAN Bus
- JTAG
- SWD
- OpenOCD
- Firmware Extraction
- EEPROM Analysis
- Secure Boot
