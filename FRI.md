# FRI (DEEP)

**Sources:** Continue with the original FRI paper, Ben-Sasson, Bentov, Horesh & Riabzev, [*Fast Reed-Solomon Interactive Oracle Proofs of Proximity*](https://eccc.weizmann.ac.il/report/2017/134/) (free, ECCC, already on your list). Reread it now with Coding Theory freshly in place; the soundness proof section specifically should read very differently than it did before.

---

## Fast Reed-Solomon Interactive Oracle Proof (full name, precisely)
Worth unpacking the name itself now that you have the vocabulary: it's an **Interactive Oracle Proof** (your §12 notes) for **Reed-Solomon** proximity (your Coding Theory notes) that's **fast** — O(log n) rounds via **folding**. Every word in the name is now a concept you actually understand, not just a label.

## Polynomial Folding, Degree Reduction
Already built — each round takes a polynomial and produces a new one of **half the degree**, by combining even/odd-indexed coefficients using a random challenge. This is the mechanical core of your implementation.

## Evaluation Domains (recap)
Fully established from your FFT notes — FRI's domain halves each round, giving the O(log n) round count from your Recurrences notes.

## Random Challenges
The randomness used both to define each folding step and to select query positions — connecting to your Probability notes (independence assumptions) and your Fiat-Shamir notes (in the non-interactive version, these come from hashing the transcript).

## Query Phase
The verifier's spot-check step — a concrete instance of **local proximity testing** (your Coding Theory notes) — querying a small random sample of positions rather than the whole committed evaluation set.

## Commitment Phase
Committing to each round's folded polynomial evaluations via **Merkle trees**, giving the transparent, hash-based security profile that distinguishes FRI from KZG/IPA.

## Merkle Authentication (recap)
Fully established — O(log n) proof size per query, from your Graph Theory notes.

## Low-Degree Testing (recap)
Fully established from Coding Theory — this is the actual problem FRI solves.

## Soundness Analysis — now precise, thanks to Coding Theory
This is where the new foundation pays off directly. FRI's soundness rests on: **(1)** Reed-Solomon's MDS distance property (a function is either genuinely low-degree, or *far* from every low-degree polynomial — no meaningful middle ground), **(2)** concentration bounds guaranteeing a small random query sample reliably distinguishes these two cases, and **(3)** the folding structure ensuring that if the *original* function was far from low-degree, the folded function stays detectably "wrong" at each subsequent round rather than the error washing out. The precise soundness bound (probability a cheating prover survives all queries) is a function of the code's **rate** (your Coding Theory notes — 1/blow-up-factor) and the **number of queries** — this is a real, calculable tradeoff, not a hand-wave: lower rate (higher blow-up) or more queries both directly increase soundness, at the cost of prover time / proof size respectively.

## DEEP-FRI (recap)
Fully established from your AIR notes — the out-of-domain-point enhancement closing a real soundness gap in vanilla FRI's linking between trace and composition polynomials.

## FRI Optimizations — practical, worth knowing exist
- **Higher-arity folding**: instead of folding 2-to-1 each round, folding 4-to-1 or 8-to-1 reduces the number of rounds (fewer Merkle commitments) at the cost of more work per round — a real tunable tradeoff in production implementations.
- **Batching**: proving low-degreeness of *several* polynomials simultaneously using one combined FRI instance (via random linear combination, the same recurring trick) rather than running FRI separately for each — significant proof-size savings when a system needs to commit to many polynomials (which PLONK-family and STARK systems typically do).
- **Grinding (proof-of-work)**: adding a cheap proof-of-work challenge on top of FRI to boost soundness slightly without adding more expensive queries — a practical, low-cost way to hit a target security level.
- **STIR and WHIR**: worth knowing these names exist as newer (2024-era) research improvements on FRI's efficiency — reducing query complexity and proof size further using more sophisticated folding/domain-shift techniques. Not necessary to implement, but worth recognizing if you see them referenced in current papers or company architecture docs (this connects to your Week 3 company-study work — some newer zkVM teams may reference these).

---

## The full picture, one more time, precisely
Trace/composition polynomial (Reed-Solomon codeword) → commit via Merkle tree → fold repeatedly (degree reduction, random challenges) → query a small random sample at the end → soundness guaranteed by MDS distance + concentration bounds, quantified by rate and query count → DEEP enhancement closes a specific linking soundness gap → optimizations (batching, higher-arity folding, grinding) make this practical at scale.

---

## Quick self-check — final one for this whole STARK/FRI cluster
You're ready to move on (to zkVMs) once you can, without notes:
1. Explain precisely why FRI's soundness bound is a function of both rate and query count, and what increasing each one buys you
2. Explain what "grinding" adds to a FRI-based proof system and why it's a cheap way to boost soundness
3. Explain the batching optimization and why it uses the same technique you've now seen repeatedly across multiple different topics this month
4. Narrate the full FRI pipeline from codeword to verified proof, using precise coding-theory language throughout
