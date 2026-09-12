# ZK Protocol Security — ZK Context Notes

**Almost entirely consolidation — every core definition here was established with precision back in your ZK Fundamentals, Crypto Foundations, and Predicate Logic notes. One item (Algebraic Group Model) is deliberately staying light here, per your original depth guide's explicit deferral to a later cycle — flagging that honestly rather than pretending to cover it in depth.**

**Source: continue Boneh & Shoup** — same spine reference as the rest of your security-definitions material.

---

## Already fully established — recap table

| Term | Where it's fully covered |
|---|---|
| **Completeness** | Proof by Contradiction / ZK Fundamentals notes — direct proof, honest case |
| **Soundness** | Proof by Contradiction / ZK Fundamentals notes — reduction argument, adversarial case |
| **Knowledge soundness** | ZK Fundamentals notes — extractor-based, stronger than plain soundness |
| **Zero knowledge** | Predicate Logic / ZK Fundamentals notes — precise quantifier order (∀adversary ∃simulator) |
| **Extractability** | ZK Fundamentals + Groth16 notes — the extractor concept, concretely realized for Groth16 |
| **Simulation** | ZK Fundamentals notes — the technique proving ZK, and its Groth16-specific trapdoor connection |
| **Special soundness** | ZK Fundamentals notes — the "two transcripts, same commitment, different challenge" extraction recipe |
| **Random oracle model** | Crypto Foundations notes — the idealized hash-as-random-function modeling assumption |
| **Fiat-Shamir security** | Crypto Foundations + Interactive Proofs + Security Engineering notes — including the real Frozen Heart failure mode |
| **Soundness error** | Probability notes (negligible probability) + FRI Soundness Analysis notes (concrete rate/query-count calculation) |
| **Reduction proofs** | Proof by Contradiction notes — the formal name for the "assume a break, derive a contradiction with a hard problem" pattern |

If any of these feel less precise than they should, that's the specific earlier note to revisit — this topic doesn't introduce new versions of them, just asks you to apply them "for every proof system," which by now you've already done repeatedly (Groth16, PLONK, STARKs, Halo2 all got this treatment individually).

---

## Genuinely new here

## Security Parameter
The formal variable (conventionally **λ**) controlling the strength of a cryptographic guarantee — appears throughout security definitions as "negligible in λ," "runs in time polynomial in λ." Practically, λ corresponds to things like field/group size or hash output length — increasing λ (e.g., moving from a 128-bit to a 256-bit field) strengthens every negligible-probability guarantee in the system simultaneously. This is the one formal piece of notation worth having crisp: whenever you see λ in a paper, it's this tunable strength knob, not an arbitrary variable name.

## Knowledge Assumptions
A specific **category** of computational assumption, distinct from more standard ones (Discrete Log, q-SDH): knowledge assumptions assert not just "this problem is hard to solve," but **"the only way to produce a specific kind of output is to already know certain input values"** — this is precisely the kind of assumption Groth16's knowledge-soundness proof relies on (your Groth16 notes' brief mention of "knowledge-of-exponent-style assumptions"), and it's what lets an extractor's existence be proven at all. Knowledge assumptions are considered a stronger, more controversial category than standard hardness assumptions in the cryptographic community, precisely because "I can compute X" and "I must know Y to compute X" are different kinds of claims — worth knowing this distinction exists, even without needing to construct or evaluate novel knowledge assumptions yourself.

## Algebraic Group Model (AGM) — deliberately light, per your original deferral
Worth being explicit rather than glossing over this: your depth guide flagged AGM/GGM as Tier-4, deliberately deferred material — genuinely research-adjacent, appropriate for a later study cycle rather than this one. Just enough to recognize the term: **AGM is an idealized model** (similar in spirit to the Random Oracle Model, but for group operations instead of hashes) where an adversary is assumed to only produce group elements as explicit algebraic combinations of group elements it has already seen — used to prove security for some pairing-based constructions where a proof in the plain model isn't known. If you encounter "we prove security in the AGM" in a paper this cycle, recognize it as a real, legitimate but idealized modeling choice (similar spirit to ROM) — full derivation-level understanding stays on your later-cycle list, consistent with what was flagged from the start.

---

## The instruction this topic actually contains
"For every proof system" is really asking you to have a habit, not new knowledge: whenever you meet a new proof system, run through completeness/soundness/knowledge-soundness/ZK/extractability for it explicitly, the way you already did for Groth16, and the way your PLONK/STARK/Halo2 notes implicitly did throughout the month. This topic is a checklist-forming exercise more than a content section — and you've already been running that checklist all along without necessarily naming it as such.

---

## Quick self-check
You're ready to consider this section complete once you can, without notes:
1. Explain what the security parameter λ controls, and give one concrete example of what increasing it means practically
2. Explain the distinction between a standard hardness assumption and a knowledge assumption
3. Explain, at the "recognize it, don't derive it" level, what the Algebraic Group Model is modeling and why it's structurally similar in spirit to the Random Oracle Model
4. Pick any proof system from your notes (Groth16, PLONK, or STARKs) and run through completeness/soundness/knowledge-soundness/ZK for it from memory, as a check that the "for every proof system" habit has actually stuck
