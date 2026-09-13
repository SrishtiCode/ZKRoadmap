# Zero-Knowledge Fundamentals (DEEP)

**Best source:** Continue with Justin Thaler, [*Proofs, Arguments, and Zero-Knowledge*](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) (your spine text, free). It has the definitive, modern treatment of exactly this list.

**Supplement for maximum rigor:** Oded Goldreich, [*Foundations of Cryptography, Volume 1*](https://www.wisdom.weizmann.ac.il/~oded/foc-vol1.html) (free preliminary draft on the author's own page). Genuinely worth citing specifically here, since Goldreich co-authored the original GMR paper that invented zero-knowledge proofs (from your earlier resource list). If you want to read these definitions as close to "from the source" as reasonably possible without tackling the 1985 paper directly, Goldreich's textbook treatment is the closest accessible equivalent.

---

## Witness & Statement
Reinforcing your NP Relation notes: the **statement** x is the public claim being proven; the **witness** w is the private information proving it's true. Every definition below is phrased in terms of this (x, w) pair.

## Prover & Verifier
The two parties: the **prover** holds the witness and wants to convince the **verifier** the statement is true, without the verifier learning the witness itself. The verifier's algorithm must be efficient (P, from your Complexity notes) for the whole system to be useful.

## Completeness, Soundness, Zero Knowledge, Knowledge Soundness
Already established precisely in your Predicate Logic and Proof Technique notes — worth a quick recall pass rather than re-deriving: completeness (direct proof, honest case), soundness (contradiction/reduction, adversarial case), zero-knowledge (simulator exists, quantifier order ∀adversary∃simulator), knowledge soundness (extractor exists, stronger than soundness alone).

## Proof vs. Argument — a real, precise terminological distinction
This is the one genuinely new formal distinction in this section, and it's more important than it looks: a **proof system** has soundness that holds even against a **computationally unbounded** cheating prover (information-theoretic soundness) — no amount of computing power helps them cheat. An **argument system** only guarantees soundness against **polynomial-time-bounded** provers — an unbounded prover theoretically *could* cheat, but no efficient one can. **This is why SNARK stands for "Succinct Non-interactive ARGUMENT of Knowledge," not "proof"** — Groth16, PLONK, and virtually everything you're building only provide computational (argument-level) soundness, resting on a hardness assumption, not the stronger information-theoretic guarantee a true "proof" would offer. This distinction is precise and deliberate in the literature — worth never being sloppy about which word you use.

## Interactive Proofs / Non-Interactive Proofs
Reinforcing from your Interactive Proofs notes: interactive proofs involve multiple rounds of prover-verifier communication; non-interactive proofs compress this to a single message, typically via **Fiat-Shamir** (already covered) or a structured trusted setup. Nearly every practical SNARK you'll implement is non-interactive, but its *design* usually starts from an interactive protocol that gets transformed.

## Simulation
The proof technique underlying zero-knowledge: constructing a **simulator** algorithm that produces a fake transcript — without ever touching the real witness — that's indistinguishable from a genuine prover-verifier interaction. This is the concrete mechanism that makes the "∀adversary ∃simulator" definition from your Predicate Logic notes actually provable for a specific protocol, rather than just stated abstractly.

## Extractors
The proof technique underlying knowledge soundness: an algorithm that, given (typically privileged/rewinding) access to a successful prover, can **extract an actual valid witness**. This is what elevates plain soundness ("a witness exists somewhere") to knowledge soundness ("this specific prover actually has one") — the extractor is the formal object that makes this stronger claim provable rather than just asserted.

## Special Soundness — the concrete mechanism behind most extractor constructions
A specific structural property common in sigma protocols: if you can obtain **two accepting transcripts that share the same first message (commitment) but differ in the challenge**, you can efficiently compute a valid witness from the two. This is usually *how* extractors are actually constructed in practice for sigma-protocol-based schemes — the extractor works by (conceptually) rewinding the prover, replaying with a different random challenge, and combining the two resulting transcripts algebraically to recover the witness. Understanding special soundness is understanding the concrete "how" behind the more abstract extractor definition.

## Honest-Verifier Zero-Knowledge (HVZK)
A **weaker**, easier-to-achieve variant: zero-knowledge is only guaranteed against a verifier who follows the protocol honestly (sends genuinely random challenges) — a malicious verifier who deviates (e.g., picks challenges adaptively based on partial information) might learn something. Many sigma protocols only achieve HVZK, not full ZK against arbitrary malicious verifiers. **This matters directly for Fiat-Shamir**: the transform typically converts an HVZK interactive protocol into a fully non-interactive zero-knowledge one, precisely because replacing the verifier's challenge with a hash function output removes the possibility of a "malicious" adaptive verifier — a hash function can't strategize, so the honest-verifier assumption becomes automatically satisfied by construction.

## Statistical ZK, Computational ZK, Perfect ZK
Applying the general security hierarchy from your Crypto Foundations notes specifically to the zero-knowledge property: **perfect ZK** (statistical distance between real and simulated transcripts is exactly 0), **statistical ZK** (distance is negligible, not necessarily exactly 0), **computational ZK** (indistinguishable only to polynomial-time-bounded distinguishers, weakest of the three but often the achievable tier for practical, efficient schemes). Same three-tier hierarchy, same ordering (perfect ⊃ statistical ⊃ computational in strength), now specifically describing how strong a given protocol's privacy guarantee is.

---

## Quick self-check before moving to Interactive Proofs (§12)
You're ready to move on once you can, without notes:
1. Explain the precise difference between a "proof" and an "argument," and state which one Groth16 actually is
2. Explain how special soundness gives you a concrete recipe for constructing an extractor, using the "two transcripts, same commitment, different challenge" idea
3. Explain why Fiat-Shamir converts an honest-verifier-ZK protocol into a fully non-interactive ZK one — what specifically about replacing the challenge with a hash makes this work
4. Explain the practical difference between statistical ZK and computational ZK, and why most efficient real-world SNARKs settle for computational ZK
