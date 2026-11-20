# riscv-cache-controller-verification
> Built near silicon- hehe literally (HDL, RTL simulation & FPGA fabric in Vivado) and geographically (Fremont, CA).

A SystemVerilog testbench and data cache (D-cache) project for RISC-V that checks whether modified data gets written back to memory safely when the cache kicks a line out while memory is stalled- built as I self-teach HDL and work toward running it on an FPGA board in Vivado.

## What This Project Does

When a processor runs code, it reads and writes data constantly. Main memory is slower than the processor, so a **data cache** acts as a small, fast notepad right next to the CPU.

This project builds a D-cache and a test environment to make sure a write-back cache doesn't lose or scramble data specifically when:
1. The cache runs out of room and needs to **evict** (kick out) old data.
2. That data was **modified** ("dirty") while sitting in the cache, so it must be saved back to main memory first.
3. Main memory is busy and **stalls** (says "not ready yet") right in the middle of that write-back.

## What I'm Testing

- **Dirty Eviction During a Stall:** Kicking out a modified cache line while main memory holds up the write-back (`ready = 0` / stall cycles).
- **Stop-and-Go Memory Timing:** Random stalls and delays across read/write signals to catch timing or handshake bugs.
- **Partial / Byte-Level Writes:** Updating individual bytes or half-words (`SB`, `SH`, `SW`) before eviction so only the right bytes and dirty flags are tracked.
- **Busy / Overlapping Requests:** New CPU reads or writes arriving while an eviction is still waiting on stalled memory.
- **Cache Flush / Fence:** Making sure all modified data finishes writing back to memory when a flush or fence happens.

## How the Testbench & FPGA Flow Is Set Up

- **Self-Taught HDL & Vivado FPGA Target:** Learning SystemVerilog RTL and verification from the ground up, simulating in Vivado, and bringing the design up on a Vivado-supported FPGA board.
- **Interface (`cache_if`):** Wires connecting the CPU side, the D-cache, and the memory side with clean clock timing.
- **Stimulus Driver:** Sends directed test cases and randomized reads/writes to stir up edge cases.
- **Stall-Injecting Memory Model:** A simulated memory that randomly stalls or slows down responses so I can test backpressure on demand.
- **Scoreboard & Memory:** Keeps a clean copy of memory and checks every read and write-back against it.
- **Assertions (SVA):** Automatic rules that flag bad states—like signals changing illegally while stalled or `X` (unknown) values leaking onto control lines.

## Timeline

- **Projected Window:** Spring 2027 – Early May 2027 *(working near silicon—self-teaching HDL, testing in Vivado, and being out in Fremont, CA)*
- **Plan:**
  1. Learn core HDL/SystemVerilog basics, define the cache rules and stall signals, and build a basic D-cache design.
  2. Build the testbench skeleton, stall memory model, and scoreboard checker.
  3. Run dirty-eviction stall tests, byte-level write tests, and assertions.
  4. Measure test coverage, squash bugs, test/target a Vivado FPGA board, save waveforms, and wrap up documentation.

## Planned Folder Layout

```text
riscv-cache-controller-verification/
├── rtl/          # D-cache logic and bus/stall wrapper
├── tb/           # Testbench components, stall memory, and scoreboard
├── sva/          # Protocol and state assertions
├── tests/        # Directed and randomized test scenarios
├── vivado/       # Vivado project scripts, constraints, and FPGA board files
└── sim/          # Simulation scripts and waveform files
