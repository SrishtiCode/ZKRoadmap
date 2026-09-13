# Modern Proof Systems (SKIM)

**Deliberately SKIM-level, per the topic's own instruction: understand what problem each solves differently, don't memorize internals. Systems you've already studied at full depth are marked (✓ deep) with just a one-line recap; genuinely new entries get a short "what's distinctive" note.**

**Source for the new entries:** Justin Thaler, [*Proofs, Arguments, and Zero-Knowledge*](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) (free, full text). Covers Marlin, Sonic, Ligero, and Bulletproofs comparatively in its historical-context sections. No new source needed, this is the same spine text doing survey-level work now instead of deep-dive work.

---

## Already deep — one-line recap only

| System | What it solves differently |
|---|---|
| **Groth16** ✓ deep | Smallest possible proof (3 group elements), circuit-specific trusted setup, pairing-based. |
| **PLONK** ✓ deep | Universal/updatable setup via Polynomial IOP + custom gates + permutation argument. |
| **Halo / Halo2** ✓ deep | Recursion without pairings, via accumulation; typically IPA-based, transparent. |
| **Nova / SuperNova / HyperNova** ✓ deep | Efficient IVC via folding (not full recursive verification); progressively generalizes uniform→non-uniform→CCS. |
| **STARKs / FRI** ✓ deep | Transparent, hash-based, plausibly post-quantum; trace+AIR+low-degree testing. |
| **Plonky3** ✓ deep | Modular STARK toolkit; SIMD-friendly small fields for real hardware performance. |
| **ProtoStar** ✓ deep | Extends folding schemes to full PLONKish constraints (custom gates, lookups). |
| **Spartan** ✓ deep (from Sumcheck notes) | Proves R1CS via sumcheck instead of QAP/pairings — transparent core, no FFTs. |

---

## New entries — the actual additions this pass

### Marlin
A **universal, updatable-setup SNARK** predating PLONK's dominance — proves R1CS satisfiability (not a PLONKish system) using a Polynomial IOP compiled with a polynomial commitment scheme. Historically important as one of the systems that established the "universal setup via Polynomial IOP" pattern PLONK later popularized further. If PLONK is the system you'll actually encounter in production, Marlin is worth knowing as an intellectual ancestor in the same design lineage.

### Sonic
Another early **universal/updatable setup** SNARK, predating and directly motivating Marlin and PLONK. Sonic's main historical contribution was demonstrating that updatable universal setups were achievable at all — later systems (Marlin, PLONK) improved efficiency and practicality on the same core idea. Worth knowing the lineage: **Sonic → Marlin → PLONK**, each iteration improving practicality on the shared "universal setup" insight.

### Plonky (the original)
The first in the Plonky lineage (predating Plonky2/Plonky3) — combined PLONK's arithmetization with FRI-based commitments instead of KZG, an early exploration of "PLONK's constraint system, STARK's transparent commitment scheme." Mostly of historical interest now — Plonky2 and Plonky3 (which you've studied) are the systems that actually matured this hybrid approach into something widely deployed.

### Plonky2
The direct predecessor to Plonky3 — introduced the **Goldilocks field** (your FFT/NTT notes) and combined PLONK-style arithmetization with FRI, specifically optimized for **fast recursion**. Plonky3 generalized and modularized Plonky2's ideas (multiple field choices instead of just Goldilocks, more flexible toolkit design) — worth knowing Plonky2 as "Plonky3, but for one specific field and less modular," rather than an unrelated system.

### Ligero
An early, conceptually important **transparent** proof system using a different underlying technique — interleaved Reed-Solomon codes (connecting to your Coding Theory notes) rather than FRI's folding approach. Notable for simplicity and being among the first practical transparent (no trusted setup) constructions, though generally less efficient in practice than FRI-based systems for large computations. Worth knowing as "an alternative transparent approach that predates and partly inspired FRI-based systems," rather than something in active competition with STARKs today.

### Bulletproofs
A **transparent**, non-succinct-verifier (verification is O(n), not O(log n) or O(1)) proof system built on **Inner Product Arguments** — the same IPA family underlying Halo2's default commitment scheme. Bulletproofs' key historical contribution was demonstrating a genuinely practical, trusted-setup-free construction with reasonably small proof sizes, particularly influential for confidential transaction / range-proof applications (its name comes from being "short like a bullet" relative to earlier range-proof constructions). Directly the intellectual ancestor of the IPA machinery you studied in Halo2.

### Bulletproofs+
An efficiency improvement over Bulletproofs — smaller proofs and faster verification for the same core IPA-based approach, particularly for range proofs specifically. Worth knowing as "Bulletproofs, optimized," not a different architecture.

### Brakedown
A **transparent** polynomial commitment scheme notable for having a very **fast, concretely efficient prover** — achieving this partly by relaxing some properties other schemes insist on (e.g., accepting linear rather than logarithmic proof size in exchange for much faster proving), using linear-time-encodable error-correcting codes (connecting again to your Coding Theory notes, a different code family than Reed-Solomon). Represents a genuinely different point on the "proof size vs. prover speed" tradeoff spectrum — useful context for understanding that "smaller proof" isn't always the optimization target real systems care about most.

### Orion
Builds on Brakedown-style techniques, pushing further on the **prover-speed-first** philosophy — part of a broader research trend (alongside Brakedown) exploring what becomes possible if you're willing to accept larger proofs in exchange for dramatically faster proving, relevant for very large computations where proving time dominates practical usability more than proof size does.

---

## The organizing insight for this whole list
Notice the real story isn't 20 unrelated protocols — it's a small number of **design axes**, with each named system representing a specific point in that space:
- **Setup**: circuit-specific (Groth16) vs. universal (PLONK, Marlin, Sonic) vs. transparent (STARKs, Bulletproofs, Ligero, Brakedown, Orion, Halo2-IPA)
- **Underlying technique**: pairings (Groth16, KZG-based PLONK) vs. IPA (Bulletproofs, Halo2) vs. FRI/codes (STARKs, Plonky2/3, Ligero, Brakedown, Orion) vs. sumcheck (Spartan, HyperPlonk, HyperNova)
- **Optimization target**: smallest proof (Groth16) vs. fastest recursion (Plonky2/3, Nova family) vs. fastest proving (Brakedown, Orion) vs. no trusted setup (everything transparent)

Once you place each system on these axes rather than memorizing its internals, "what problem does X solve differently" answers itself — which is exactly the instruction this topic came with.

---

## Quick self-check
You're ready to move on once you can, without notes:
1. Trace the lineage Sonic → Marlin → PLONK and explain what each iteration improved
2. Explain what Plonky2 is relative to Plonky3, in one sentence
3. Explain the core tradeoff Brakedown/Orion make (proof size vs. prover speed) and why that tradeoff is sometimes the right one
4. Place any three systems from this list on the three design axes (setup, technique, optimization target) without looking back at the table
