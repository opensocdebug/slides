# Open SoC Debug

## Overview

Stefan Wallentowitz - November 12, 2015

---

## What is Open SoC Debug

 * Hardware building blocks for SoC Debugging

 * Infrastructure for debug systems for system-on-chip around those building blocks

 * Basic host libraries to interact with the debug system

---

## Why Open SoC Debug

 * Benefit from existing building blocks

 * Interoperability with other SoCs

 * Efficient debug systems become more important

---

## System Overview

Modular design, clear interfaces, composability

<img src="{{site.assets}}/images/overview_150dpi.png"
  style="background:none; border:none; box-shadow:none;"/>

---

## Physical Transport

 * Not defined: Physical interface and protocols

 * Use [Generic Logic Interface Project (glip)](http://glip.io) as transport

  * One common interface for different interfaces
  
  * Wrappers around open source interfaces

  * Currently: USB 2/3 (with Cypress FX), JTAG, PCIE

  * Simple FIFO interface, usually 16-bit

---

## Transport Interface

Hardware:

    wire [WIDTH-1:0] data;
    wire valid;
    wire ready;

Software:

    void send(size_t len, uint16_t* data);

    // poll
    int receive(size_t maxlen, uint16_t* data);

    // callback
    register_callback(void (*recv)(size_t maxlen, uint16_t* data));

---

## Debug Packets

 * Control, data and events are transmitted as packets

 * Each module and the host have a unique ID

 * Basic Format:

<img src="{{site.assets}}/images/debug_packet_150dpi.png"
  style="background:none; border:none; box-shadow:none;"/>

---

## Debug Network-on-Chip

 * Packets are routed over a NoC

 * Baseline is a simple, uni-directional ring

  * Low cost

  * Arrange positions with traffic demands

 * Each module and host are participants

---

## Debug Modules

TODO

---

## Core Debug Modules

TODO

---

## Core Trace Modules

TODO

---

## Trace Triggering

TODO

---

## Debug Processors

TODO

---

## Trace Recording

TODO

---

## Other Debug Modules

TODO