# Recursive Proofs — ZK Context Notes

**You already have three concrete worked examples in hand: Halo's accumulation, Cairo's "prove the verifier as a program," and zkVM continuations. This section is mainly about formalizing the vocabulary that unifies them, plus one genuinely important precise distinction (composition vs. aggregation) worth nailing.**

**Sources:**
1. **Continue the Halo2 Book** (already used) — its recursion sections address this formally.
2. **Valiant's original IVC paper** ("Incrementally Verifiable Computation, or Proofs of Knowledge Imply Time/Space Efficiency") — for the formal origin of the IVC concept.
3. **Preview the Nova paper's introduction** now, since Folding Schemes (§30) is your very next topic — Nova's intro formally defines IVC in the specific framing modern folding schemes use, and reading it now will make §30 land faster.

---

## Recursive SNARKs
A SNARK whose **statement is itself about the validity of another proof** — "a proof that a proof is valid." The recursive structure can chain arbitrarily deep: proof C verifies proof B, which itself verified proof A, and so on.

## Proof Composition
The general umbrella term — combining or chaining proofs together in some structured way, where one proof's validity relates to another's. This covers recursion specifically, but also other combination patterns more broadly.

## Proof Aggregation — worth a precise distinction from recursion
**Genuinely different from recursion, despite often being mentioned in the same breath**: aggregation means combining **many independent proofs into one smaller, more efficiently verifiable result** — but this doesn't necessarily require one proof's circuit to contain a full verifier for another proof. Sometimes aggregation is achievable via simpler **batching techniques** (your Batch Opening / Multi-Opening notes from Cryptographic Commitments) rather than true recursive in-circuit verification. **The precise distinction**: aggregation is about "many independent proofs → one compact result" (which can sometimes be done cheaply, without full recursion); recursion specifically means "proof A's circuit literally contains verification logic for proof B." Not every aggregation technique is recursive, even though recursion is one way to achieve aggregation.

## Proof Recursion
The specific technique that earns the "recursive" name: a circuit explicitly contains **in-circuit verification logic** (below) for another proof. This is what makes Cairo's "prove the verifier as a Cairo program" approach and Halo's accumulation both genuinely *recursive* techniques, as opposed to simpler aggregation.

## In-Circuit Verification — the expensive operation everything is designed around
Implementing a SNARK or STARK **verifier's logic as constraints inside another circuit** — genuinely expensive, because verifier operations (pairing checks, FRI's Merkle/query checks) need to be **simulated using arithmetic constraints**, which is far more costly than just running the verifier natively. This expense is precisely **why** accumulation schemes and continuations exist — both are strategies for avoiding doing full in-circuit verification at every single step.

## Curve Cycles (recap)
Already fully established from your Elliptic Curves notes — the EC-level trick (matching base/scalar fields between a pair of curves) that makes in-circuit pairing-based verification tractable, avoiding expensive non-native field arithmetic simulation. Directly relevant whenever recursion involves pairing-based proofs specifically.

## Accumulation Schemes — Halo's mechanism, now generalized
Already deeply understood from your Halo notes as a specific instance — now recognize the **general pattern**: instead of fully verifying a claim immediately, **defer it by folding/accumulating it into a growing claim**, checked fully and expensively only **once, at the very end**. This general pattern is the conceptual ancestor of **folding schemes** (Nova/SuperNova, your very next topic) — Halo's specific accumulation mechanism is one instance of this broader "accumulate now, verify later" family.

## Incrementally Verifiable Computation (IVC) — the formal name for the actual goal
This is the term that unifies everything you've encountered this month under one formal definition: a prover proves a **long-running computation incrementally** — producing, at each step, a proof that's efficiently verifiable and that attests to the **entire computation so far** (not just the latest step), **without needing to redo verification of all previous steps from scratch**. This is precisely, formally, what:
- **zkVM continuations** achieve in the zkVM context (your zkVM notes)
- **Cairo's "prove the verifier as a program"** achieves via direct recursive verification
- **Halo's accumulation** achieves via deferred folding

All three are different **techniques** for achieving the same formal **goal** — IVC. Recognizing this is genuinely valuable: you now have three concrete, different implementations of one unifying concept, rather than three separate unrelated facts.

## Recursive STARKs
Recursion applied specifically in the STARK context — proving a STARK verifier's correctness (FRI checks, Merkle authentication checks) as another STARK-provable computation. This is exactly Cairo's approach in practice, and more generally describes any STARK-based recursive system. Worth noting: STARK-based recursion **doesn't face the curve-cycle problem** (no pairings involved at all), but has its own overhead — representing FRI's query/Merkle verification logic in-circuit is relatively expensive compared to representing a single pairing check, even without the curve-mismatch issue pairing-based recursion deals with.

## Recursive SNARKs (recap, with the tradeoff now explicit)
Pairing-based recursive SNARKs face the curve-cycle challenge specifically (needing carefully matched curve pairs to avoid non-native arithmetic); STARK-based recursion avoids that specific problem but pays a different cost (FRI verification logic being comparatively expensive to arithmetize). Neither approach is free — this is a genuine, real engineering tradeoff every recursive system design has to navigate.

---

## Your three worked examples, now formally unified
| Technique | Achieves IVC via... | Faces which cost? |
|---|---|---|
| **Cairo's recursion** | Direct in-circuit STARK-verifier-as-program | FRI verification arithmetization cost |
| **Halo's accumulation** | Deferred folding, full check only at the end | IPA-specific accumulation algebra, avoids full in-circuit verification per step |
| **zkVM continuations** | Segment-boundary proofs chained via recursive verification | Whatever the underlying zkVM's proving system costs (RISC Zero/SP1 = STARK-based; inherits STARK recursion tradeoffs) |

---

## Quick self-check before moving to Folding Schemes
You're ready to move on once you can, without notes:
1. Explain the precise distinction between proof aggregation and proof recursion — why is aggregation not always recursive?
2. Explain what makes in-circuit verification expensive, and name two different strategies (from your existing notes) that avoid doing it in full at every step
3. State the formal definition of IVC, and map each of your three worked examples (Cairo, Halo, continuations) onto it as different techniques for the same goal
4. Explain the different costs faced by pairing-based recursive SNARKs versus recursive STARKs
