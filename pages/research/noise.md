---
layout: page
title: Noise Analysis of Superconducting ADCs - Research Overview
subtitle: Robustness of an oversampling, phase-modulation-demodulation S-ADC at cryogenic temperatures
tags: [superconducting, RSFQ, ADC, cryogenic, noise, robustness]
---

> TL;DR - During my internship at Lawrence Berkeley National Laboratory (LBNL), I built a Simulink-based framework to analyze how different noise mechanisms impact a superconducting ADC (S-ADC) that uses phase modulation-demodulation. By injecting stochastic bit-flip and timing-jitter errors at multiple points in the signal path and running Monte Carlo sweeps, I quantified which blocks are most vulnerable and what design choices harden the system.

![Figure 1 - Simulink model overview of the S-ADC](../../../assets/img/research/sadc_fig1.png)
*Figure 1 from the paper. A block-level view of the model used in the robustness study.*

## What problem I set out to solve
S-ADCs enable in-cryostat digitization for quantum sensors and other cryogenic systems, reducing analog cabling and improving signal integrity. But internal and external noise sources (e.g., thermal variation, flux trapping, jitter, radiation) can degrade fidelity. The goal was to identify where the S-ADC fails first, how failure manifests in SNDR/ENOB, and what inexpensive mitigations help most.

## Architecture in brief
- Quantizer (sigma-delta modulator): Uses Josephson Junction (JJ) pulse generation to encode the input differential. 1-bit, oversampling.
- Synchronizer: Integrates differential pulses to form a digital code; implemented as an array of phase-shifted comparators with accumulation.
- Decimation filter: CIC-style downsampling for low-complexity, JJ-friendly implementation.

![Figure 2 - Error injection mechanisms](../../../assets/img/research/sadc_fig2.png)
*Figure 2 from the paper. Two injected noise types: bit-flip and timing jitter.*

## Methodology
- Error models: Bit-flip (sign inversion) and timing jitter (variable time delay). Errors are selected from uniform draws vs. a threshold; true -> inject error, false -> pass-through.
- Injection points: Multiple locations along the chain (e.g., quantizer output, synchronizer/accumulator, moving average, downsampler, discrete derivative).
- Simulation setup: 10 GHz sampling, sinusoidal input near a divided Nyquist, 20 runs per threshold with consistent seeds per sweep for fair comparisons.
- Metrics: SNDR and ENOB. SNDR computed from output spectra using a 4-term Blackman-Harris window with 8 guard bins.

## Results highlights
![Figure 4 - Spectral comparison: low vs high error probability](../../../assets/img/research/sadc_fig4.png)
*Figure 4 from the paper. Frequency-domain view showing SNDR collapse at higher error rates.*

![Figure 6 - ENOB vs noise threshold by error location](../../../assets/img/research/sadc_fig6.png)
*Figure 6 from the paper. Location-dependent robustness; decimation path is most sensitive.*

## Key findings
1. Location matters. Sensitivity is highly location-dependent. The decimation path dominates overall robustness when all sources are active.
2. Decimation blocks are bottlenecks. The downsampler and moving-average stages degrade at error probabilities as low as about 1e-6 in our experiments, well before front-end elements.
3. Quantizer and accumulator are comparatively robust. They maintain higher ENOB until the error threshold rises (order 1e-4 in our tests), with downstream filtering masking some front-end noise.
4. Sharp knee beyond a critical threshold. ENOB declines gradually, then falls off rapidly once errors exceed a component-specific limit.
5. Input amplitude interacts with robustness. ENOB improves with amplitude up to a limit before other factors dominate.
6. Mitigation helps where it matters. Triplication with majority voting (TMR) applied to the most sensitive blocks (e.g., discrete derivative, downsampler) shifts failure cutoffs to lower error rates and yields higher SNDR at a given threshold.

## Why this matters
For practical cryogenic systems, total error budget is shaped more by where noise enters than by a single global rate. Hardening the decimation chain (or relocating part of it closer to robust stages) can buy significantly more ENOB than over-optimizing already-robust front-end blocks.

## Acknowledgment
This work was performed during my internship at Lawrence Berkeley National Laboratory (LBNL).

---
