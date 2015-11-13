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

## Transport Interface (glip)

Hardware:

    wire [WIDTH-1:0] data;
    wire valid;
    wire ready;

Software:

    void glip_send(size_t len, uint16_t* data);

    // poll
    int glip_receive(size_t maxlen, uint16_t* data);

    // callback
    glip_register_callback(void (*recv)(size_t maxlen, uint16_t* data));

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

 * Common interface: NoC packets

 * Modules can consume and produce packets

 * Convenience wrapper: Memory-mapped registers

 * Common basic functions: version, capabilities, enumeration

 * Share a timebase via a globally distributed counter

---

## Core Debug Modules

 * Implement traditional run-control debugging

  * Control functions: Run, step, reset

  * Data functions: Read and write registers

  * Breakpoint functions: Set and enable

  * Generate breakpoint event

---

## Core Trace Modules

 * Emit debug events of execution trace

 * Configurable
 
  * On each instruction, branch, etc.

  * Compression mechanism

  * Set trigger conditions to reduce trace sizes

---

## Trace Cross-Triggering

 * Correlate trigger events

 * Ease debugging of complex systems

  * "Trace this core if the DMA takes to long"

  * "Trace the interconnect load during synchronization events"

 * Separate trigger network for precise timing correlation

---

## Debug Processors

 * Debug modules with an entire subsystem

 * Configure trace events to go there

 * Aggregate and filter events

 * Small microporgrams to genrate information from data

 * Can generate trace events or even control messages

---

## Trace Recording

 * Standard: online debugging

 * Possible multiplexed trace targets, e.g.,

  * Trace to SD card

  * Trace to system memory

---

## System Control Module

 * Common module to all open soc debug systems

 * Platform/Chip identifier

 * Possibly clock gating for emulation mode

 * Overflow configuration:

  * Gate clocks (care needed with respect to I/O)

  * Drop packets and notify host about this

---

## Other Debug Modules

 * Trace and debug devices

 * Memory access module (read, write memory)

 * Interconnect load traces

 * ...

---

## Host Interface

 * Lowest level: Send a packet to a debug module

```
void osd_send(uint16_t module, uint8_t class,
             size_t len, uint16_t *packet) {
    int16_t dbgpacket[MAX_DBGPKT_LEN];
    assert((len+2) <= MAX_DBGPKT_LEN);

    dbg_packet[0] = module << 6;
    dbg_packet[1] = (OSD_HOST_ID << 6) | (class & 0x1f);
    memcpy(&dbg_packet[2], packet, len);

    glip_send(len + 2, dbg_packet);
}
```

---

## Host Interface: Memory-mapped

```
void osd_mm_read(uint16_t module, uint16_t address,
                 uint16_t *value) {
   int rv;
   int16_t packet[1];

   packet[0] = address;
   osd_send(module, OSD_CLASS_MMRD, 2, packet);

   osd_receive(module, OSD_CLASS_MMRD, 1, packet);
   *value = packet[0];
}
   
```

---

## Host Debug Software

 * opensocdebug library as interface to debug infrastructure

  * Low-level and memory-mapped functions

  * APIs for different debug modules

 * Integrate with (open) debug software

---

# Get involved

Looking for people to discuss, write spec and contribute.

[stefan@wallentowitz.de](mailto:stefan@wallentowitz.de)