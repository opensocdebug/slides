# Open SoC Debug @ lowRISC 

## Prototyping Phase 1

Stefan Wallentowitz, Wei Song - May 22, 2016 

---

## Goals

- Flexible debug infrastructure

 - Re-usability for other projects

 - Layered design

- Focus on trace debugging

 - Observe SoC minimally intrusively

 - Trace hardware and software execution

- Run-control debugging planned later this year

---

## lowRISC Debug System Architecture

<img src="{{site.assets}}/images/overview_lowrisc_phase1.svg"
  style="background:none; border:none; box-shadow:none;"/>

---

## Data Exchange between Host and Target

- Based on the *Generic Logic Interface Project (glip)*

 - Abstract from physical interface, transparent transport

 - Simple FIFO interface on host and target

 - Transport can be JTAG, UART, USB, TCP, PCIe, Aurora, etc.

- For Nexys4 board: UART at 3 MBaud (300kbit/s)

- For KC705 board: USB3 with Cypress FX3 (wip)

---

## Interfacing the RTL Simulation

<img src="{{site.assets}}/images/overview_lowrisc_RTLsim.svg"
  style="background:none; border:none; box-shadow:none;"/>

- Full system simulation (Compiled RTL simulation with Verilator)

- Direct Programming Interface (DPI) with TCP socket

---

## Interfacing the FPGA

<img src="{{site.assets}}/images/overview_lowrisc_FPGAuart.svg"
  style="background:none; border:none; box-shadow:none;"/>

- "High-speed" UART replaces the current UART interface

- The system UART is replaced with a device emulation module, UART over debug

---

## Debug Interconnect

<img src="{{site.assets}}/images/overview_lowrisc_ring.svg"
  style="background:none; border:none; box-shadow:none;"/>

- Uni-directional ring: good speed, minimal footprint

- Planned: two rings (control/run-control and trace), hierarchical interconnect

---

## System Control

- Allow the host to reset the system

- Separate system reset from core reset to allow halted cores

- Implemented by System Control Module (SCM)

---

## Core Tracing

- Trace core execution information (can be much information)

- Currently used to generate

 - Function trace (enter and leave of C functions)

 - Mode changes (user and machine execution level)

- Implemented by Core Trace Module (CTM), Rocket core trace interface

---

## Software Tracing

- Instrument software with trace points

- Each software trace event is a tuple (`id`, `value`)

- Used to measure performance or functional debugging

- Minimally intrusive (register write and CSR write)

- Implemented by Software Trace Module (STM), Rocket core extended

---

## Memory Access

- Directly access memory

 - Inspect and update memory content

 - Load ELF file

- Implemented as Memory Access Module (MAM)

- Connects to the second level cache

---

## UART Device Emulation

- AXI/NASTI interface identical to UART 16550

- transport characters between host and module as debug packets

- Seamlessly replaces the existing UART module

---

## Host Software

- libopensocdebug: low level API and abstraction APIs 
 
- Debug applications 

	- Bind single application to debug target

	- Command line interface, python scripting

	- Run `opensocdebugd` daemon and connect multiple applications to
	modules

---

## Demo: Connect to the Simulation

- Start RTL simulation

        # ./DebugConfig-sim

- Start daemon, it enumerates the system at start

        # opensocdebugd tcp

---

## Demo: Connect to the FPGA (Nexys4)

- Download the bitstream

        # make program

- Start daemon with different backend and options

        # opensocdebugd uart -o device=/dev/ttyUSB1,speed=3000000

---

## Demo: Command Line Interface

- Command line interface is the most simple access to the system

        # osd-cli
        >

---

## Demo: System Control

- Reset the system (core and uncore)

        > reset

- Reset the system, but keep reset of cores high

        > reset-halt

- De-assert the reset of the cores

        > start

---

## Demo: Use UART

- Start a terminal for the device emulation module at id 2

        > terminal 2

- This opens an xterm window

---

## Demo: Log the Function Trace

- Configure trace

        > reset -halt
        > ctm log ctm.log 4
    	> start

- The output file (`ctm.log`) contains function trace (plus timestamps)

        00033c7c enter init_tls
        00033cac enter memcpy
        00033d48 enter memset
        00033dd1 enter thread_entry
        00033e41 enter main
        00033e67 enter uart_init
    	...

---

## Demo: Instrument Code for Software Trace

- Simple macro

        #define STM_TRACE(id, value) \
          { \
            asm volatile ("mv   a0,%0": :"r" ((uint64_t)value) : "a0");	\
            asm volatile ("csrw 0x8f0, %0" :: "r"(id));	\
          }

- Register `a0` is observed by STM (first argument register)

- Write to CSR triggers the actual event

---

## Demo: Generate Software Trace

- Configure trace

        > reset -halt
        > stm log stm.log 5
		> start

- The output file contains the tuples plus timestamps

        000012549 0001 deadbeef
	    000013f45 0023 98765432
		...

- Needs further interpretation

---

## Python Interface

- Python bindings included

- Used with cli at startup

        # osd-cli -p -s startup.py
        > 

- Used with cli in batch mode

        # osd-cli -p -b debug.py

- Used from python

        # ./debug.py

---

## Example Python Script

    import opensocdebug 
    import sys

    if len(sys.argv) < 2:
        print "Usage: runelf.py <filename>"
        exit(1)

    elffile = sys.argv[1]
    
    osd = opensocdebug.Session()

    osd.reset(halt=True)

    for m in osd.get_modules("STM"):
        m.log("stm{:03x}.log".format(m.get_id()))

    for m in osd.get_modules("CTM"):
        m.log("ctm{:03x}.log".format(m.get_id()), elffile)

    for m in osd.get_modules("MAM"):
        m.loadelf(elffile)

    osd.start()
    osd.wait(10)

---

## Outlook

- Trace filtering and triggering

- Run-control debug support (wait for RISC-V spec)

- Tool support
