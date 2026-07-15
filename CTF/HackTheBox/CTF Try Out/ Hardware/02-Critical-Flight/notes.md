# Notes - Critical Flight

> Quick revision notes for PCB Reverse Engineering, Gerber Files, and Hardware Security.

---

# Challenge Summary

- **Category:** Hardware
- **Difficulty:** Very Easy
- **Platform:** Hack The Box – CTF Try Out

---

# Important Concepts

## Printed Circuit Board (PCB)

A PCB (Printed Circuit Board) is the foundation of almost every electronic device.

It provides:

- Mechanical support
- Electrical connections
- Signal routing
- Power distribution

Instead of manually connecting wires, copper traces are etched onto the board.

---

# PCB Structure

Typical PCB Stack

```
Top Silkscreen

──────────────

Top Soldermask

──────────────

Top Copper

──────────────

Dielectric

──────────────

Inner Copper

──────────────

Dielectric

──────────────

Bottom Copper

──────────────

Bottom Soldermask

──────────────

Bottom Silkscreen
```

---

# PCB Layers

## Copper

Purpose

- Carries electrical signals
- Connects components
- Forms power rails
- Creates communication paths

Without copper, the PCB cannot function.

---

## Soldermask

Usually green.

Purpose

- Protects copper
- Prevents oxidation
- Prevents accidental short circuits
- Improves durability

---

## Silkscreen

Usually white.

Purpose

- Component labels
- Pin numbers
- Logos
- Reference names

This layer carries **no electrical signal**.

---

## Drill Layer

Contains all drilled holes.

Used for

- Component leads
- Mounting holes
- Vias

---

## Outline Layer

Defines the physical shape of the PCB.

Used during manufacturing.

---

# Gerber Files

Gerber files are manufacturing instructions for PCB fabrication.

Each file represents one layer.

Examples:

```
Top Copper

Bottom Copper

Top Silkscreen

Bottom Silkscreen

Top Soldermask

Bottom Soldermask

Outline

Drill
```

A Gerber Viewer combines all layers to reconstruct the complete PCB.

---

# Copper Layer

Most important layer.

Responsible for:

- Signal transmission
- Power distribution
- Ground planes

Security analysts should always inspect copper carefully.

---

# Hardware Steganography

Steganography means hiding information inside another object.

Examples:

- Images
- Audio
- Documents
- PCB Layers

Instead of hiding data in a picture, this challenge hides information inside PCB manufacturing layers.

---

# Hardware Supply Chain

PCB modifications may introduce:

- Hardware Trojans
- Hidden traces
- Covert components
- Spy circuits
- Debug interfaces

This is why hardware verification is important.

---

# Gerber Viewer

Purpose:

Visualize PCB manufacturing files.

Useful Features:

- Hide layers
- Show copper
- Show silkscreen
- Show drill
- Inspect internal layers
- Zoom into traces

---

# Investigation Checklist

When receiving Gerber files:

✅ Identify all layers

✅ Open using Gerber Viewer

✅ Inspect top layer

✅ Inspect bottom layer

✅ Toggle layers individually

✅ Compare copper layers

✅ Inspect internal copper

✅ Search for hidden markings

---

# Solving Methodology

```
Receive Files

      │

      ▼

Identify Gerber Files

      │

      ▼

Open Gerber Viewer

      │

      ▼

Inspect PCB

      │

      ▼

Hide Layers

      │

      ▼

Inspect Copper

      │

      ▼

Locate Hidden Information
```

---

# Real-World Applications

Knowledge from this challenge applies to:

- Hardware Security
- PCB Reverse Engineering
- Supply Chain Verification
- Counterfeit Hardware Detection
- Embedded Systems
- Electronics Manufacturing

---

# Common Mistakes

❌ Looking only at the top layer

❌ Ignoring bottom copper

❌ Ignoring internal layers

❌ Assuming silkscreen contains all information

❌ Never hiding individual layers

---

# PCB Components

Common labels seen on PCBs:

MCU → Microcontroller

USB → USB Connector

GPS → GPS Module

FLASH → Flash Memory

SWD → Serial Wire Debug

PWM → Pulse Width Modulation

IMU → Inertial Measurement Unit

EEPROM → Electrically Erasable Programmable ROM

---

# Useful Websites

PCBWay Gerber Viewer

https://www.pcbway.com/project/OnlineGerberViewer.html

---

# Interview Questions

## What is a PCB?

A Printed Circuit Board that mechanically supports and electrically connects electronic components.

---

## What are Gerber Files?

Manufacturing files that describe every PCB layer.

---

## What is the purpose of Copper?

Electrical signal transmission.

---

## What is the purpose of the Soldermask?

Protects copper and prevents solder bridges.

---

## What is Silkscreen used for?

Component labels and markings.

---

## Why inspect Copper Layers?

Because hidden traces or messages can exist beneath the soldermask.

---

## What is Hardware Steganography?

The practice of hiding information inside hardware design files such as PCB manufacturing layers.

---

# Quick Revision

Remember:

PCB

↓

Gerber Files

↓

Gerber Viewer

↓

Layer Analysis

↓

Copper Inspection

↓

Hidden Information

---

# Revision Checklist

- [ ] Understand PCB Structure
- [ ] Know Gerber Files
- [ ] Understand Copper Layers
- [ ] Understand Soldermask
- [ ] Understand Silkscreen
- [ ] Understand Drill Layer
- [ ] Know Hardware Steganography
- [ ] Understand Supply Chain Attacks
- [ ] Use Gerber Viewer
- [ ] Analyze PCB Layers Systematically

---

# Personal Notes

Things I learned:

- A PCB consists of multiple manufacturing layers.
- Gerber files contain the complete PCB design.
- Hidden information can exist inside copper layers.
- Hardware security includes inspecting manufacturing files, not just firmware.
- A systematic layer-by-layer analysis is more effective than randomly searching for hidden text.

---

# Next Topics

After this challenge, continue learning:

- UART
- Logic Analyzer
- Saleae Logic
- Embedded Systems
- SPI
- I²C
- JTAG
- SWD
- Firmware Reverse Engineering
