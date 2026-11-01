# Two Boards, One Host: Running Muse Glimmer on a PCIe-Coupled Pair of Arduino Ventuno Q Computers

**Christopher Maglio and Muse — October 31, 2026**

This paper discusses a possible solution to the current "reasonable cost" local AI hardware problem. The Arduino Ventuno Q as a standalone machine cannot run the 4-bit builds of 30B-class models such as Muse Glimmer or Qwen3.8-27B — its 16 GB of memory is the binding constraint. However, the Ventuno Q has two features that may make it possible to run a pair of them as a two-node, tightly coupled cluster. First, its M.2 PCIe slot connects directly to its processor module, making the low latency possible that tensor-style splitting depends on. Second, its onboard microcontroller can supervise and coordinate the Linux master/client relationship. The paper details what would have to be built and calculates the performance that would result.

## Contents

- `Ventuno-PCIe-Pair-Glimmer-2026-10-31.pdf` — the paper (12 pages).

## Summary of contents

- **The system (§2–3):** two Ventuno Q boards joined by an ADT-Link PCIe 4.0 x4 TX/RX-swap cable between their M.2 slots; one board as PCIe root complex (master), the other as a PCIe endpoint presenting a Non-Transparent Bridge (client), pooling 32 GB of memory for inference via llama.cpp RPC over `ntb_netdev`, with the two STM32H5 microcontrollers supervising power, reset, and recovery.
- **What must be created (§4):** ten work items (W1–W10), each with a description and a probable implementation route — cable power isolation, clock and reset role assignment, the endpoint-mode device tree and kernel builds, bring-up sequencing, the microcontroller coordination firmware, inference-layer integration, mechanical/thermal integration, and a validation harness.
- **Calculated performance (§5):** Meta's official Glimmer builds are 4-bit only (Q4_K_M 16.8 GB, Q4_K_XL 19.7 GB); community 6-bit (~23.0 GB) also fits the pair, while 8-bit (~29.7 GB) is capacity-infeasible. Calculated decode at Q4_K_M is 2.0–2.4 tok/s, rising to an extrapolated 3.4–5.3 tok/s with DFlash speculative decoding, calibrated against a measured dual-Tesla-P100 llama.cpp build. Prefill is identified as the weak direction.

## Status

Design study. The system described has not been built; all performance figures are calculations or labelled extrapolations, not measurements. The gating experiment is identified in §6: one board enumerating as the other's PCIe endpoint.
