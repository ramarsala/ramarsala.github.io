---
layout: page
title: Hybrid SFQ/CMOS Simulation -- Research Overview
subtitle: End-to-end architectural modeling for superconducting systems
# Optional tags for your site generator
# tags: [RSFQ, superconducting, cryogenic, RISCV, memory, interconnects]
---

> TL;DR -- I built an endtoend, **tracedriven** simulation framework for **hybrid RSFQ/CMOS systems** that models the processor, cryogenic memories, and interconnects across **4 K, 77 K, and 300 K**. It lets me answer questions like: *Where should caches live inside the cryostat? When do cable lengths dominate? How much does an L0 SFQ cache help?* Along the way I surfaced several design insights for future superconducting architectures.

![Figure 2 -- Simulator components overview](../../../assets/img/research/sim_fig2.png)
*Figure 2 from the paper. A highlevel view of the simulator blocks and dataflow.*

## What I built
- A **RISCV, tracedriven execution core** based on RSFQ gatelevel pipelining behavior (inorder), suitable for sweeping clock frequencies while keeping realistic controlflow effects.
- A **hybrid memory model** that plugs in **CryoMEM/CACTIderived** timings for CMOS memories at 4 K/77 K/300 K and accommodates future RSFQ caches.
- **Interconnect analytics** for cryostat wiring and converters, capturing:
  - Temperaturezone hops (4 K<->77 K<->300 K)
  - Converter stacks (Suzuki vs. SQUID) and their **rate limits**
  - **Propagation delay** vs. **serialization** effects
  - **Channel count** and its bandwidth/thermal tradeoffs
- A **configurable cache hierarchy**, including an optional **ultrasmall L0 SFQ cache** at 4 K to study locality close to the processor.

## Why SFQ/CMOS is tricky
Superconducting RSFQ logic can clock very fast at cryogenic temperatures, but the **memory-compute gap** widens when data has to cross temperature regions and logic families. Interfacing RSFQ pulses to CMOS DC signals needs **converter stacks**, and long cryostat cables add latency that typical roomtemperature simulators don't model.

![Figure 1 -- Memory placement options inside a cryostat](../../../assets/img/research/sim_fig1.png)
*Figure 1 from the paper. Placing memory at 4 K, 77 K, or 300 K trades latency, bandwidth, and thermal budget.*

## How the simulator works
- **Processor model.** Uses an RSFQaware pipeline and decodes **RV32IMA** traces (Spikecompatible) so I can evaluate realistic programs while isolating memory behavior.
- **Memory model.** Each level (L0/L1/L2/... at different temperatures) gets **read/write** latencies derived from CryoMEM/CACTI and combines **fixed access** + **bits transferred** to match burst sizes and line sizes.
- **Interconnect model.** Adds wire **propagation** and **serialization** delay plus converter constraints. **Suzuki** stacks favor lower heat but lower data rate; **SQUID** stacks push higher data rate at the cost of amplification/thermal load. Cable **length** and **channels** parameterize bandwidth vs. space/heat.

![Figure 3 -- RSFQ<->CMOS interconnect stacks](../../../assets/img/research/sim_fig3.png)
*Figure 3 from the paper. Where Suzuki and SQUID stacks sit in the path and how data flows across temperature zones.*

## Key findings (highlevel)
1. **A tiny L0 SFQ cache at 4 K pays off.** Even at a few hundred bytes, placing an L0 next to the processor yielded sizable IPS gains (~1.5x-4.5x depending on the trace) and improved frequency scaling at high clocks.
2. **Interconnects dominate earlier than you think.** Converter rate limits and long 4 K<->77 K cables can cut throughput sharply; in nonL0 setups the penalty can approach ~half the performance of "ideal nointerconnect" baselines.
3. **Optimal cacheline size depends on whether L0 exists.** With an L0 at 4 K, smaller lines (e.g., 16 B) helped certain memoryintensive traces; without L0, the processor often talks directly to warmer memory, shifting the sweet spot.
4. **Read/write asymmetry matters with SQUID stacks.** Higher RSFQ->CMOS data rate makes **writes** much faster than **reads** in some setups, so workload R/W mix influences converter choice.
5. **Cable length and channel count are firstorder design knobs.** Performance degrades with length; more channels help but cost area/thermal budget. Cryostat geometry meaningfully shapes systemlevel throughput.

![Figure 6 -- Cache hierarchies with vs. without a 4 K L0 cache](../../../assets/img/research/sim_fig6.png)
*Figure 6 from the paper. Where the interconnect sits relative to the L0 changes what traffic hits the coldest stage.*

## Results snapshots
- **Cable length sensitivity (77 Konly memory):** As 4 K<->77 K length grows, propagation delay pushes cycles up by ~an order of magnitude in some traces, underscoring the need for any 4 Kresident cache.

  ![Figure 7 -- Impact of 4 K<->77 K cable length](../../../assets/img/research/sim_fig7.png)
  *Figure 7 from the paper. Relative performance vs. a nocable baseline.*

- **Converter choice and latency asymmetry:** Suzuki (ratelimited by CMOS) yields similar read/write latency; SQUID (ratelimited by RSFQ/memory) skews faster for writes than reads.

  ![Figure 8 -- Read vs. write latencies (Suzuki vs. SQUID)](../../../assets/img/research/sim_fig8.png)
  *Figure 8 from the paper. Direction matters when the converter, not the memory, sets the data rate.*

## Why this matters
This framework gives architects a way to **codesign processor, memory, and cryostat**. Instead of assuming "faster core = faster system," we can quantify when **interconnects and temperature placement** bottleneck performance and how small **cold caches** reshape the picture. It also creates a path to evaluate **future SFQ memories** and **multicore** scenarios with realistic wiring limits.

## What's next
- Multicore RSFQ evaluation and synchronization costs
- Energy/thermal models integrated with latency/bandwidth
- Exploring codesigned **optoelectrical cryogenic links** and fixedfunction superconducting blocks

---

### Notes for the web build
- The image placeholders above are set to `../../../assets/img/research/`. Replace them with your actual exported figures from the paper.
