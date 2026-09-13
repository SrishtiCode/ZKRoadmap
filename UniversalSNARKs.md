# Universal SNARKs (SOLID)

**Sources:**

1. Gabizon, Williamson & Ciobotaru, [*PLONK: Permutations over Lagrange-bases for Oecumenical Noninteractive arguments of Knowledge*](https://eprint.iacr.org/2019/953) (free, IACR ePrint, already on your reading list). Directly motivates and defines universal/updatable setup as part of its core contribution.

2. Zcash Foundation, [*Conclusion of the Powers of Tau Ceremony*](https://zfnd.org/conclusion-of-the-powers-of-tau-ceremony/) (free), plus Sam Parker, [*The Power of Tau, or: How I Learned to Stop Worrying and Love the Setup*](https://medium.com/zeroknowledge/the-power-of-tau-or-how-i-learned-to-stop-worrying-and-love-the-setup-535a05bec15d) (free). For grounding the abstract concept in how a real, large-scale ceremony actually runs, including how the two-phase (Powers of Tau → circuit-specific) structure works in practice.

---

## Universal Setup — solving Groth16's real limitation
A trusted setup that works for **any circuit up to some maximum size**, rather than being tied to one specific circuit's exact structure. This is the direct fix for the practical problem you just identified in Groth16: instead of running a new ceremony every time a circuit changes, you run **one ceremony once**, and it remains usable for every future circuit within the size bound.

## Updatable Setup
A refinement making universal setups even more practical: **anyone can contribute additional randomness to an existing setup at any time**, further strengthening its security (in the same "1-out-of-n honesty" sense as your Groth16 ceremony notes) **without needing to coordinate a single, one-time, all-participants-at-once ceremony**. This is a meaningful practical improvement — the original Powers of Tau ceremony for Zcash-adjacent projects allowed exactly this kind of ongoing, asynchronous participation, letting the community continuously improve the setup's trust assumptions over time rather than being stuck with whatever the initial ceremony participants contributed.

## Structured Reference Strings (SRS) — recap, now generalized
The general term for the public parameters produced by a trusted setup — you've already worked with this concept directly (your KZG SRS, Groth16's proving/verification keys). The key shift in this section: a **universal** SRS is structured so it doesn't encode anything circuit-specific (no uᵢ(τ), vᵢ(τ), wᵢ(τ) baked in) — it's just **powers of the secret evaluation point τ**, usable as a building block for committing to *any* polynomial up to a bounded degree, regardless of which circuit that polynomial eventually comes from.

## Powers of Tau
The specific, most common form a universal SRS takes: a sequence of group elements encoding τ⁰·G, τ¹·G, τ²·G, ..., τ^d·G (and similarly in G₂ where needed) for some maximum degree d. This is literally the same structure as your KZG SRS — **a universal SRS is, in essence, exactly a large KZG-style SRS**, general-purpose rather than tied to one circuit's specific polynomials. "Powers of Tau" is also the name commonly used for the actual large-scale, multi-party ceremonies that generate these parameters in production (the Zcash/Ethereum-adjacent ceremonies you may have heard referenced).

## Universal CRS vs. Circuit-Specific CRS
The direct contrast this whole section is built around: a **circuit-specific CRS** (Common/Structured Reference String) — what Groth16 uses — encodes structure tied to one particular circuit's QAP, requiring a new ceremony per circuit. A **universal CRS** encodes nothing circuit-specific — just raw powers of τ — and can be reused across unlimited different circuits (up to the size bound), with the circuit-specific structure instead getting folded in later, at proving/verification time, rather than baked into the setup itself.

## Polynomial Commitment-Based SNARKs
The architectural pattern that *enables* universal setup: if your SNARK is built as "commit to some polynomials, prove evaluations" (exactly the Polynomial IOP + Polynomial Commitment Scheme framework from your Interactive Proofs notes), then the trusted setup only needs to support the **commitment scheme** generically (powers of τ, usable for committing to *any* polynomial) — it never needs to know what those polynomials will represent for any particular circuit. This is exactly why PLONK, Marlin, Sonic, and other Polynomial-IOP-based SNARKs naturally support universal setup, while Groth16 — built directly around one specific circuit's QAP rather than a generic polynomial-commitment layer — structurally cannot.

---

## The connection tying this whole section together
Universal setup isn't a separate trick bolted onto PLONK — it's a **direct architectural consequence** of building a SNARK from the Polynomial IOP + Polynomial Commitment Scheme pattern instead of Groth16's circuit-specific QAP-and-pairing construction. Once you see that connection, "why does PLONK have universal setup and Groth16 doesn't" stops being a memorized fact and becomes an obvious consequence of the two systems' fundamentally different architectures.

---

## Quick self-check before moving to PLONK
You're ready to move on once you can, without notes:
1. Explain precisely why a universal SRS can be reused across different circuits, while Groth16's proving key cannot
2. Explain what an updatable setup adds beyond a plain universal setup, and why this matters practically
3. Explain the architectural reason (Polynomial IOP + Commitment Scheme) that PLONK naturally supports universal setup while Groth16 structurally cannot
4. Explain what "Powers of Tau" refers to, both as a mathematical object and as a real-world ceremony
