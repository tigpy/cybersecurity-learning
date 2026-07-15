# Critical Flight

> **Category:** Hardware  
> **Platform:** Hack The Box – CTF Try Out  
> **Difficulty:** Very Easy  
> **Primary Concepts:** PCB Reverse Engineering, Gerber Files, Copper Layers, Hardware Steganography

---

# Overview

This challenge introduces **Printed Circuit Board (PCB) reverse engineering** using **Gerber files**.

Unlike traditional reverse engineering, there is no executable program to analyze. Instead, the challenge provides the manufacturing files used to fabricate a PCB. The objective is to inspect different PCB layers and identify suspicious modifications hidden within the board design.

This challenge demonstrates how attackers can conceal information inside hardware manufacturing files and highlights the importance of hardware verification during the production process.

---

# Learning Objectives

After completing this challenge, I was able to:

- Understand how PCBs are designed.
- Learn what Gerber files are.
- Understand the purpose of each PCB layer.
- Navigate PCB manufacturing files.
- Inspect individual PCB layers.
- Understand hardware steganography.
- Identify hidden information inside copper layers.

---

# Skills Covered

- PCB Reverse Engineering
- Gerber File Analysis
- PCB Layer Inspection
- Copper Layer Analysis
- Hardware Steganography
- Embedded Hardware Fundamentals
- Supply Chain Security

---

# Files Provided

The challenge contained multiple **Gerber (.gbr)** files representing the manufacturing data for a PCB.

Typical files included:

- Top Copper
- Bottom Copper
- Inner Copper Layers
- Top Silkscreen
- Bottom Silkscreen
- Top Soldermask
- Bottom Soldermask
- Drill Files
- Outline Layer

These files collectively describe the complete PCB.

---

# Background Concepts

## What is a PCB?

A Printed Circuit Board (PCB) mechanically supports and electrically connects electronic components.

Instead of manually wiring components together, conductive copper traces are etched onto a board.

A PCB provides:

- Mechanical support
- Electrical connections
- Signal routing
- Power distribution

---

## PCB Stack-Up

A multilayer PCB consists of several layers stacked together.

Example:

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

Each layer has a specific purpose.

---

# What are Gerber Files?

Gerber files are the industry standard used by PCB manufacturers.

They describe every manufacturing layer of the board.

Each layer is stored separately.

When combined together, they recreate the complete PCB.

---

# Important PCB Layers

## Copper

Purpose:

Electrical connections.

Copper traces connect:

- Microcontrollers
- Sensors
- ICs
- Connectors
- Power rails

Without copper, the PCB cannot function.

---

## Soldermask

Usually the green layer.

Purpose:

- Protect copper
- Prevent oxidation
- Prevent accidental solder bridges
- Improve durability

---

## Silkscreen

White printed labels.

Purpose:

- Component names
- Pin numbers
- Logos
- Reference designators

This layer is purely informational.

---

## Drill Layer

Contains every drilled hole.

Used for:

- Component leads
- Mounting holes
- Vias between layers

---

## Outline Layer

Defines the PCB shape.

Manufacturers use this during fabrication.

---

# Hardware Steganography

Steganography means hiding information inside another object.

Examples:

- Images
- Audio
- Documents
- Videos

This challenge demonstrates **Hardware Steganography**.

Instead of hiding information inside an image, data is hidden inside PCB manufacturing layers.

---

# Challenge Methodology

The objective was **not** to immediately search for hidden text.

Instead, a structured hardware analysis approach was followed.

---

## Step 1

Identify the provided files.

Determine whether they represent:

- Source code
- Firmware
- Schematics
- PCB manufacturing data

The presence of multiple `.gbr` files indicated Gerber manufacturing files.

---

## Step 2

Open the board inside a Gerber Viewer.

A Gerber Viewer reconstructs the complete PCB by combining every manufacturing layer.

This makes it possible to inspect the board exactly as a PCB manufacturer would.

---

## Step 3

Inspect the Top Layer.

Check:

- Component labels
- Connectors
- Reference designators

Nothing suspicious was immediately visible.

---

## Step 4

Inspect the Bottom Layer.

Compare:

- Silkscreen
- Copper
- Labels

The board appeared mostly normal.

---

## Step 5

Hide Individual Layers.

Instead of viewing the complete PCB, disable layers one by one.

Example:

```
Copper

ON

Silkscreen

OFF

Soldermask

OFF

Drill

OFF
```

Removing unnecessary layers makes hidden markings easier to identify.

---

## Step 6

Inspect Copper Layers

The hidden information existed only on specific copper layers.

This demonstrates that manufacturing files can intentionally contain information invisible during normal inspection.

---

# Investigation Workflow

```
Receive Gerber Files

          │

          ▼

Identify PCB Layers

          │

          ▼

Open Gerber Viewer

          │

          ▼

Inspect Top Layer

          │

          ▼

Inspect Bottom Layer

          │

          ▼

Toggle Individual Layers

          │

          ▼

Inspect Copper Layers

          │

          ▼

Identify Hidden Information
```

---

# Key Takeaways

- Gerber files describe PCB manufacturing data.
- Every PCB layer serves a unique purpose.
- Hidden information may exist only on certain manufacturing layers.
- Hardware verification is an essential security process.
- Supply chain attacks may involve modified PCB layouts.

---

# Real-World Relevance

PCB analysis is used in:

- Hardware Security
- Embedded Security
- Reverse Engineering
- Military Electronics
- Aerospace
- Industrial Control Systems
- Supply Chain Verification

Potential threats include:

- Hardware Trojans
- Counterfeit Electronics
- Malicious PCB Modifications
- Hidden Components
- Covert Copper Traces

---

# Tools Used

- PCBWay Online Gerber Viewer
- Gerber Files
- Web Browser

---

# Lessons Learned

This challenge demonstrates that hardware security extends beyond firmware and software.

The physical design itself may contain hidden functionality or concealed information.

Security analysts should verify not only firmware but also manufacturing files before trusting hardware.

---

# Common Mistakes

❌ Looking only at the completed PCB.

❌ Ignoring inner copper layers.

❌ Inspecting only the silkscreen.

❌ Assuming hidden information must be visible.

---

# Real Investigation Checklist

Whenever analyzing Gerber files:

- Identify all provided layers.
- Understand the PCB stack-up.
- Inspect top and bottom separately.
- Toggle visibility of individual layers.
- Compare copper layers.
- Look for unusual traces or markings.
- Inspect hidden internal layers.
- Verify drill and outline consistency.

---

# References

## PCB Fundamentals

https://learn.sparkfun.com/tutorials/pcb-basics

## Gerber File Format

https://www.ucamco.com/en/gerber

## PCB Design

https://www.allaboutcircuits.com/

## PCBWay Gerber Viewer

https://www.pcbway.com/project/OnlineGerberViewer.html

---

# Next Topics

After understanding PCB analysis, the next hardware concepts to study are:

- UART
- Logic Analyzer
- Saleae Logic
- SPI
- I²C
- JTAG
- SWD
- Firmware Extraction
- Embedded Debugging

---

# Summary

This challenge introduced PCB reverse engineering through Gerber manufacturing files.

By understanding the purpose of each PCB layer and systematically inspecting them individually, it became possible to identify hidden modifications that would otherwise remain unnoticed.

The exercise reinforces an important lesson in hardware security:

> **Never trust the assembled board alone—verify the manufacturing data as well.**

---

> **Note:** This repository is maintained for educational purposes. The focus is on understanding PCB architecture, Gerber files, and hardware security methodologies rather than simply documenting challenge solutions.
