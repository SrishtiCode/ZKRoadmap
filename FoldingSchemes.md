# Folding Schemes (SOLID)
**Conceptual fluency is the right target here — understand the mechanism and why it works well enough to explain it, without needing full implementation-level derivation this month. You're arriving with accumulation schemes and IVC already formalized, which makes this land fast.**

**Sources:**

1. Kothapalli, Setty & Tzialla, [*Nova: Recursive Zero-Knowledge Arguments from Folding Schemes*](https://eprint.iacr.org/2021/370) (free, IACR ePrint). You've already previewed the intro; now read through the core folding construction.

2. For each extension, focus on "what new capability does this add," not full derivation:
   - Kothapalli & Setty, [*SuperNova: Proving Universal Machine Executions Without Universal Circuits*](https://eprint.iacr.org/2022/1758) (free, IACR ePrint)
   - Kothapalli & Setty, [*HyperNova: Recursive Arguments for Customizable Constraint Systems*](https://eprint.iacr.org/2023/573) (free, IACR ePrint)
   - Bünz & Chen, [*Protostar: Generic Efficient Accumulation/Folding for Special-Sound Protocols*](https://eprint.iacr.org/2023/620) (free, IACR ePrint)

---

## Folding — the core operation
Taking **two instances** of a (relaxed) constraint system and combining — "folding" — them into **one new instance**, such that the new instance is satisfiable **if and only if** (with overwhelming probability, via the same random-linear-combination trick you've now seen many times) **both original instances were satisfiable**. The key payoff: verifying this fold is dramatically **cheaper** than fully verifying both original instances separately — you're compressing two verification obligations into one, cheap check, deferring full certainty to a single expensive check at the very end (exactly the accumulation pattern from your Recursive Proofs notes).

## Relaxed R1CS — Nova's key technical innovation
Standard R1CS, as you've built it, does **not** have a useful closure property under folding — combining two valid R1CS instances via a naive linear combination generally does **not** produce another valid R1CS instance. Nova's actual innovation is **Relaxed R1CS**: a modified version of R1CS that adds a **slack/error term** and a **scaling factor** to the standard (A·z)⊙(B·z)=(C·z) equation. This relaxation is specifically engineered so that **folding two relaxed R1CS instances together produces another valid relaxed R1CS instance** — a genuine closure property standard R1CS lacks. This single technical device is what makes the whole Nova construction possible — worth treating as the one piece of "new math" in this section actually worth sitting with, even at conceptual level.

## Nova — the architecture, tied to what you already know
Uses Relaxed R1CS + the folding operation + one continuously-updated **accumulated instance**, achieving IVC where the verifier's per-step work is just **O(1) folding work** (cheap group operations — essentially a linear combination, connecting to your MSM/Linear Combinations notes) rather than full R1CS verification at every step, **plus one final, more expensive verification only at the very end** of the whole chain. This is precisely the general accumulation-scheme pattern from your Recursive Proofs notes — now with **concrete, specific algebraic machinery** (Relaxed R1CS folding) rather than Halo's IPA-based mechanism. Same philosophy, different technical realization.

## SuperNova — extending to non-uniform computation
Nova's original construction assumes the same function/circuit is being run repeatedly at every step. **SuperNova extends this to non-uniform IVC** — proving computations that can **switch between different circuit types** at each step, rather than repeatedly running one fixed function. This connects directly to your zkVM notes: **different CPU instructions are effectively different "functions"** being executed at each trace step, and SuperNova's non-uniform extension is directly relevant to folding-scheme-based zkVM designs, where each step might need to fold a *different* instruction's constraint system depending on what opcode is being executed — the folding-scheme-level analog of your zkVM's selector-based CPU constraint dispatch.

## HyperNova — extending to CCS, worth revisiting after Sumcheck
Extends folding to work with **CCS (Customizable Constraint System)** — a more general constraint system than R1CS, closer in expressiveness to PLONKish/AIR-style generality — and incorporates **sumcheck-based techniques** directly. **Worth flagging explicitly: HyperNova will make substantially more sense once your dedicated Sumcheck/Multilinear Algebra deep-dive (§31–32) is done** — if HyperNova's mechanics feel hazy right now, that's expected and not a gap in this pass; revisit it specifically after Sumcheck.

## ProtoStar — extending to full PLONKish generality
A further generalization making folding schemes work efficiently with **general PLONKish constraints** — custom gates and lookups (your §18–19 notes) — rather than being limited to R1CS-style constraints specifically. This represents the folding-scheme family converging toward compatibility with the full modern PLONKish toolkit you've spent real time building this month.

## Incremental Computation, Accumulation, IVC (recap)
Fully established from your Recursive Proofs notes — folding schemes are, concretely, **the current state-of-the-art practical technique** for achieving IVC, which is exactly why this research family (Nova onward) has become so central to modern ZK system design in just the past few years.

## Folding Schemes (umbrella term)
Nova, SuperNova, HyperNova, and ProtoStar are all specific constructions within this category — each extending **applicability** (uniform → non-uniform → CCS → full PLONKish) or improving **efficiency**, but all sharing the same core "fold two instances into one, defer full verification" philosophy.

---

## The progression, one more time, as a single picture
**Nova** (R1CS, uniform computation, introduces the relaxed-R1CS folding trick) → **SuperNova** (adds non-uniform computation — different circuits per step, directly relevant to zkVMs) → **HyperNova** (generalizes to CCS, brings in sumcheck) → **ProtoStar** (generalizes further to full PLONKish constraints including custom gates and lookups). Each step in this family extends what kinds of computation can be efficiently folded, while keeping the same underlying "cheap per-step folding, one expensive final check" architecture.

---

## Quick self-check before moving to Sumcheck
You're ready to move on once you can, without notes:
1. Explain why standard R1CS lacks the closure property folding needs, and what Relaxed R1CS specifically adds to fix this
2. Explain what "non-uniform IVC" means, and why it's directly relevant to zkVM design (connecting to CPU instruction dispatch)
3. Explain, at a high level, what capability each step in the Nova → SuperNova → HyperNova → ProtoStar progression adds
4. Explain why Nova's per-step verifier cost is so much cheaper than full R1CS verification, connecting this to the general accumulation-scheme pattern from your Recursive Proofs notes
