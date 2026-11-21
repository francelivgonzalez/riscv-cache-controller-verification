# RISC-V Cache Controller Verification
> Write-back RISC-V data cache controller and SystemVerilog verification testbench with SVA assertions, stall modeling, and Digilent Cora Z7 FPGA bring-up

Built near silicon, literally and geographically hehehe. I’m planning to work on this project while I’m in Fremont, CA, teaching myself SystemVerilog and testing my design on a Digilent Cora Z7-07S.

**Status:** Planned
**Projected timeline:** January 2027 through early May 2027
**Hardware target:** Digilent Cora Z7-07S
**Tools:** SystemVerilog, SystemVerilog Assertions, and AMD Vivado / Vitis

## What I’m building

I’ll build a small write-back data cache for a RISC-V-style load/store interface and a testbench that checks it. My main question is simple: if the processor changes a value in the cache and memory is busy when the cache needs that space back, does the right value still reach the right address?

A cache line is a block of data held in the cache. A line becomes “dirty” when the processor changes it. Before the cache reuses that space, it has to preserve the modified data. That handoff is what I want to verify.

## My first test

1. Store `0xDEADBEEF` at address A.
2. Access a different address that forces the cache to evict A.
3. Make memory wait seven clock cycles before accepting the write-back.
4. Check the address and data that memory actually accepts.
5. Read A again and check that the processor gets `0xDEADBEEF`.

I’ll also run the same test with zero stall cycles. Comparing the two waveforms will help me see which signals have to stay stable while memory is waiting.

## What I’ll test

- **Dirty and clean evictions.** A dirty line should write back its updated data. A clean line should be replaceable without a write-back.
- **Partial stores.** Byte, halfword, and word stores should update exactly the intended bytes.
- **Stop-and-go memory.** I’ll vary the wait time and use repeatable random stalls on reads and writes.
- **A second CPU request.** While an eviction is pending, the controller must handle the next request according to its interface rules. I’ll check that every accepted request completes exactly once.
- **Explicit cache flush.** If I implement a flush command, I’ll verify that it writes back dirty lines before reporting completion.

## How I’ll check it

The testbench will include a request driver, a memory model that can insert stalls, and a scoreboard that keeps track of expected values. Assertions will check the interface rules every clock cycle. For example, when a write is valid and memory is waiting, the write address and data must remain stable until the transfer is accepted.

On the Cora Z7, I’ll use a small hardware test circuit to issue requests and recreate the seven-cycle stall. That lets me exercise the cache on the FPGA without building a full processor first.

## Timeline

- **January:** Learn the SystemVerilog basics, define the cache size and interface, and get one store/load test passing.
- **February:** Implement refill and eviction. Build the memory model and scoreboard.
- **March:** Test stalled write-backs, partial stores, and requests arriving while the cache is busy.
- **April through early May:** Run repeatable regressions, capture waveforms, and bring the main test onto the Cora Z7.

## Results I plan to publish

A test summary, commands to reproduce each test, an annotated dirty-eviction waveform, and the FPGA result. I’ll also deliberately introduce one write-back bug and show the exact cycle where my checker catches it.

## Planned folders

- `rtl/` - cache controller and memory interface
- `tb/` - driver, memory model, and scoreboard
- `sva/` - protocol assertions
- `tests/` - directed and randomized test cases
- `vivado/` - project scripts and Cora Z7 constraints
- `results/` - waveforms, test summaries, and board results


