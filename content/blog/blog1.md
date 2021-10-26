---
title: "[Seminar Summary] RISC-V SoC hardware security testing by HDL analysis [EN]"
date: 2021-10-25T12:49:27.000+07:00
featureImage: images/blog/blog-seminar-summary.jpg
postImage: https://lh3.googleusercontent.com/pw/AM-JKLWwSfN2b2IzCRT37TYfF3_dR7AYq9WDJZ4bOlkrVrZEOk-nvOp_XSFJSVU1svIb7T_bSKCDNe54svpOkGCO9j3KbGiTz0zv_evsn_JLttJAdOvW6tIpoZA6_XX2ZpSr9pZPRO60HKGo76_bwQmd8j39=w1080-h432-no?authuser=0
imageUrl: https://lh3.googleusercontent.com/pw/AM-JKLWwSfN2b2IzCRT37TYfF3_dR7AYq9WDJZ4bOlkrVrZEOk-nvOp_XSFJSVU1svIb7T_bSKCDNe54svpOkGCO9j3KbGiTz0zv_evsn_JLttJAdOvW6tIpoZA6_XX2ZpSr9pZPRO60HKGo76_bwQmd8j39=w1080-h432-no?authuser=0
author: Le Khac Minh Dang
---
> We held an online seminar recently on 15/10/2021 with the topic "RISC-V SoC hardware security testing by HDL analysis" by Le Khac Minh Dang. This is the summary of the seminar contents by the speaker. - Modifier.


## Overview
This topic is about doing HW security testing by hunting for bugs on a RISC-V SoC named Openpiton by analysing its RTL source code:
1. Simulate the Openpiton SoC
2. Analyse its RTL source code to rebuild its block diagrams
3. Dig deep into each module to hunt for bugs
4. For each bugs: write testbench -> write exploit proof of concept -> assess its severity
5. Suggest a patch to fix each of the bugs
6. Give a conclusion about HW security and HW vulnerabilities, …

### Why this topic?
**Subjective reasons:**
- HW security is an new and interesting field to learn.
- HW security is often overlooked by security researchers, even though its severity is the same, if not more, than SW.

**Objective reasons:** The topic requires all the skills that I’ve learnt over the years in university:
- HW concepts + Verilog programming
- Reverse engineering
- Exploitation

## Background knowledge
### About the target
**Scope:** The target is a modified version of the open-sourced Openpiton SoC with Ariane core, introduced in **hack@CHES 2021** competition. 

**Why this specific version?**
- **Simplicity:** only 2 cores – one for main execution and one for DMA.
- **Realistic:** the original version is barebones, this version is added with real-world peripherals.
- **Specialized:** the design is injected with real-world bugs inspired by past CVEs.
- **Educational:** this topic is mainly for educational purpose, not to hunt for bug bounties or credits.

### About Openpiton
- Source: [https://github.com/PrincetonUniversity/openpiton](https://github.com/PrincetonUniversity/openpiton)
- An open source, general purpose, multithreaded manycore SoC (up to half a billion cores).
- Highly configurable in both core and uncore components.
    - Originally developed with a 64-bit SPARC processor.
    - Now supports the 64-bit Ariane RISC-V processor (our target).
- Capable of running popular Linux OSes (Ubuntu, Debian, …).
- Can be simulated with popular simulators (Verilator, ModelSim, Synopsys VCS, …).
- Can be synthesized to FPGAs.

### About Ariane
- Source: [https://github.com/openhwgroup/cva6/](https://github.com/openhwgroup/cva6/)
- A 6-stage, single issue, in-order CPU.
- Fully implements I, M, A and C RISC-V extensions.
- Fully implements three privilege levels M, S, U to fully support a Unix-like operating system.
- Compliant to the draft external debug spec.

### About RISC-V
- Open standard ISA based on RISC principles.
- Completely free to use.
- Is a load-store architecture.
- Has a modular design, alternative base parts, with optional extensions.
- Documentations:
    - [https://github.com/riscv/riscv-isa-manual](https://github.com/riscv/riscv-isa-manual)
    - [https://github.com/riscv/riscv-debug-spec](https://github.com/riscv/riscv-debug-spec)
- RISC-V base sets:
    - RV32I: 32 registers, 32-bit register size and address space.
    - RV32E: 16 registers, 32-bit register size and address space.
    - RV64I: 32 registers, 64-bit register size and address space.
    - RV128I: 32 registers, 128-bit register size and address space.
    - All base sets have 32-bit instructions.
- RISC-V extensions:
    - M extension: for multiplication, division and modulo.
    - A extension: for atomic operations (load/store) on shared memories.
    - F/D/Q extensions: for floating-point operations with single/double/quad precision.
    - C extension: for 16-bit compressed instructions.

### About hardware security bugs
Physical hardware bugs:
- Example: power glitching, clock glitching, temperature-based attacks, …
- Require physical access to the device.
- May require specialized tools to successfully exploit.
- Exploitations are usually inconsistent.

Software-exploitable hardware bugs:
- Example: iOS checkra1n, Intel Spectre and Meltdown, …
- Doesn’t require access or tools.
- Can be exploited remotely if the device is running a service.
- Exploitations are usually consistent.

The main goal of this topic is to look for software-exploitable hardware bugs, in order to:
- Identify the bugs.
- Suggest a realistic case where the bugs may be encountered in real world.
- Suggest a systematic way to find, exploit, fix these type of bugs.
- Assess the severity of the vulnerabilities.

## Simulation
- Simulation environment is on an Ubuntu 20.04 VM.
- Simulation tool is Verilator.
- Openpiton provides a ready-to-use simulation script.
- Only simulate a single C user application, instead of the whole OS.
- Takes 10-15 minutes to finish running a program.

## Analysis
The whole Openpiton project can be broken down to 4 parts for analysis:
- Openpiton’s uncore (lowest priority)
- Ariane’s RISC-V core
- Ariane’s peripherals (highest priority)
- Proxy kernel (not a part of the hardware)

Difficulties in the analyzing progress:
- Openpiton and Ariane both have very limited documentations.
- Added peripherals have no documentation at all.
  - Lots of reverse engineering works are required.

### Openpiton's Uncore
- Openpiton is a manycore SoC, each core is located on a tile.
- Each tile contains an Ariane core and an Uncore part.
- An Uncore part contains things like L15 cache and NoC connections, ...
- Since accessing the caches and NoC connections are difficult from software, the Uncore part has the lowest analysis priority.

### Ariane core
- 6 stages:
  - PC Generation (frontend)
  - Instruction fetch (frontend)
  - Instruction decode
  - Issue
  - Execute
  - Commit
- Each stage has many submodules, each submodule must be analyzed separately because any of them can contain a vulnerability.

### Ariane's added peripherals
- Many peripherals are added to Ariane in this modified version, they can be divided into 3 groups:
  - Controllers: access controller, register-lock controller, reset controller.
  - Cryptography: AES0, AES1, AES2, RSA, SHA256, HMAC, PRNG
  - Others: DMA + PMP, PKT + FUSE memory, JTAG, Bootrom, PLIC, FLINT.
- Peripherals communicate with the core via AXI4Lite and Advanced Peripheral Bus (APB)
- Since accessing peripherals are simple via system calls, they have the highest analysis priority.

### Some analysis result
- Incorrect assignment bug in Register-lock controller leading to some registers will be locked when they are not supposed to, and vice versa.
- Out-of-bound register access bug in Access controller leading to a part of the access controller register will never be locked.
- FSM if-statement logic bug in the state transition combinational block of SHA256 leading to incorrect state transition and impossible to reach state.

---  


Post Image: [Neringa Hünnefeld](https://unsplash.com/@ringane) (Unplash)