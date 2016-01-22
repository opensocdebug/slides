# Open SoC Debug @ lowRISC 

## Prototyping Phase 1

Stefan Wallentowitz - January 22, 2016 

---

## lowRISC Debug Prototype Phase 1

<img src="{{site.assets}}/images/overview_lowrisc_phase1.svg"
  style="background:none; border:none; box-shadow:none;"/>

---

## Host Interface Module

- Interfaces glip and debug network
  
- Simple (de-)packetization
  
- Configuration Interface

	- Gate clock vs. drop packets

	- Read status

- Status

	- No spec, no implementation

	- Building blocks from before: [LISNoC/infrastructure](https://github.com/optimsoc/sources/tree/master/external/lisnoc/rtl/infrastructure)

---

## Debug Interconnect

- Simple unidirectional ring with flow control

- Open for many improvements (hierarchical, high-level flow control)

- Status

	- Basic router in Chisel, possibly needed as System Verilog

---

## System Control Module

- Provides system information

- Generate reset and cpu_halt signals

- Status

	- No spec, no implementation

	- Previously existed at [OpTiMSoC/TCM](https://github.com/optimsoc/sources/blob/master/src/rtl/debug_system/verilog/tcm.v)

---

## Software Trace Module

- CPU generates (id,value) tuples

- Add trace timestamp (local for the moment, see [spec issue #1](https://github.com/opensocdebug/documentation/issues/1))

- Status

	- No spec, no implementation

	- Previously existed at [OpTiMSoC/STM](https://github.com/optimsoc/sources/blob/master/src/rtl/debug_system/verilog/stm.v)

---

## Memory Access Module 

- Initialize memory, analyse memory 

- Generic bus interface and wrappers

- Status

	- No spec, no implementation

	- Previously existed at [OpTiMSoC/MAM](https://github.com/optimsoc/sources/blob/master/src/rtl/debug_system/verilog/mam.v)

---

## Device Emulation Module: UART 

- Generic bus interface plus Wrappers

- MMIO registers identical to actual hardware (UART 16550?)

- Sends characters in packets to host

- Status

	- No spec, no implementation

---

## Host Software

- libopensocdebug: low level API and abstraction APIs 
 
- Debug applications 

	- Bind single application to debug target

	- Command line interface, python scripting

	- Run `opensocdebugd` daemon and connect multiple applications to
	modules

- Status: Will be ported from [OpTiMSoC/liboptimsochost](https://github.com/optimsoc/sources/tree/master/src/sw/host/liboptimsochost)

---

## Roadmap

- First draft of basic specifications (HIM, SCM, UART): February 1st

- Possible to start implementation in parallel to further spec then

- Host porting in February

- Proposed next call: week of February 16

---

## Outlook

 - Core Trace Module: Identify relevant information, entire trace?

 - Core Debug Module: Run-control debugging, with ETH. openocd adapter?

