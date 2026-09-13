# Cryptography Foundations (DEEP) 

**Best source:** Boneh & Shoup, [*A Graduate Course in Applied Cryptography*](https://toc.cryptobook.us/) (free, full text). Already on your reading list from earlier. This is the right source specifically for this section: it formally covers security games, adversary models, and the computational/statistical/perfect security hierarchy with the exact rigor and notation you'll see in ZK papers. Since you're already planning to use this as your "spine" reference for the month, this section is where that decision pays off most directly.

---

## Security Definitions
A precise security definition specifies: what the adversary is trying to do, what resources/access they have, and what "winning" looks like — usually formalized as a **security game** (below). Vague statements like "this is secure" mean nothing in cryptography; every real claim is "secure against [specific adversary model] with respect to [specific game]." Every property you've studied so far (completeness, soundness, zero-knowledge) is a security definition in exactly this sense.

## Adversaries
A formal model of "the attacker" — typically defined by a **resource bound** (usually polynomial-time, i.e. "PPT" — probabilistic polynomial time, connecting directly to your Computational Complexity notes) and an **interface** (what they're allowed to query or observe). ZK soundness definitions quantify over *all* PPT adversaries — this is exactly the "∀ PPT adversary" pattern from your Predicate Logic notes.

## Security Games
The formal framework for defining security: a game between a challenger and an adversary, with explicit rules for what the adversary can do and what counts as a win. Soundness is phrased as a game ("adversary tries to produce a false-statement proof that verifies"); zero-knowledge is phrased as a distinguishing game ("adversary tries to tell real transcripts from simulated ones"). Reading a new protocol's security proof starts with identifying its security game precisely — get comfortable parsing these before worrying about the proof itself.

## Computational Security
Security that holds only against **efficient (polynomial-time) adversaries** — an unbounded adversary *could* break it, but no realistic attacker can. Most of what you've built (KZG's binding property, Groth16's soundness) is computational security, resting on assumptions like discrete log being hard.

## Statistical Security
A **stronger** guarantee: security holds even against **computationally unbounded** adversaries, with failure probability bounded by something negligible but not relying on any hardness assumption. Statistical zero-knowledge (from your Probability notes — statistical distance ≈ 0 between real and simulated transcripts) is an example: even an all-powerful adversary can't distinguish, because there's genuinely (almost) no statistical difference to exploit.

## Perfect Security
The strongest tier: **zero** probability of failure/distinguishing, not just negligible. Rare in practice for full protocols (usually reserved for specific sub-components, like a one-time pad's perfect secrecy) but worth knowing as the theoretical ceiling above statistical and computational security — the three form a clear hierarchy: perfect ⊃ statistical ⊃ computational, from strongest to weakest guarantee.

## Computational Assumptions
The unproven-but-widely-believed hardness claims security proofs reduce to — Discrete Log, q-SDH (used specifically in Groth16), and similar. Every computational security proof bottoms out in "assuming [X] is hard" — there's no security claim in this field that isn't ultimately conditional on some assumption, which is worth internalizing: "provably secure" always means "provably secure *relative to* an assumption," never secure in an absolute sense.

## Randomness
The literal resource every security game and every protocol run consumes — verifier challenges, Fiat-Shamir hash outputs (treated as random), prover blinding factors. Directly connects to your Probability notes (probability spaces, independence) — every security bound you compute is a statement about probability over this randomness.

## Cryptographic Hashes
Functions mapping arbitrary-length input to fixed-length output, with specific security properties (below) that make them useful as building blocks. Used constantly in ZK: Merkle tree construction (FRI, STARK commitments), Fiat-Shamir transformation (turning interactive proofs non-interactive), and in-circuit as ZK-friendly hash functions (Poseidon, etc., from your earlier depth guide).

## Collision Resistance
Hard to find two **distinct** inputs x≠y with hash(x)=hash(y). Directly connects to your Pigeonhole notes: collisions mathematically *exist* (pigeonhole guarantees this for any fixed-output hash), but finding one should be computationally infeasible. This is exactly the property that keeps Merkle trees secure — a broken collision resistance would let an attacker forge a fraudulent leaf while preserving the same root.

## Preimage Resistance
Given a hash output y, it should be hard to find *any* input x such that hash(x)=y. A distinct property from collision resistance — a hash function could theoretically have one without the other, though in practice good hash function designs aim to provide both simultaneously.

## Second-Preimage Resistance
Given a *specific* input x, it should be hard to find a *different* input x'≠x with the same hash. Subtly different from full collision resistance (which doesn't fix either input in advance) — second-preimage resistance is a weaker, more targeted guarantee. Worth knowing the three properties (collision, preimage, second-preimage resistance) as genuinely distinct, since papers are precise about which one a given argument actually needs.

## Random Oracle Model (ROM)
An idealized model where a hash function is treated as a **perfectly random function** — for every new input, imagine an oracle that outputs a uniformly random value (consistently, if queried again with the same input). This is a modeling *assumption*, not a real property any concrete hash function actually has — but it makes many security proofs tractable, and it's the standard model under which Fiat-Shamir is analyzed. Worth knowing this is a simplification that real implementations only approximate, not a literal guarantee about SHA-256 or Poseidon.

## Fiat-Shamir Heuristic — the transformation that makes SNARKs practical
Converts an **interactive** proof (multiple rounds of prover-verifier communication) into a **non-interactive** one: instead of the verifier sending a real random challenge, the prover computes the challenge themselves by hashing the protocol transcript so far. Under the Random Oracle Model, this hash output is treated as if it were a genuinely random verifier challenge. **This is exactly why SNARKs can be single-message proofs** rather than requiring live back-and-forth interaction — Groth16, PLONK, and virtually every practical SNARK relies on this transformation somewhere in its construction or in how its underlying interactive protocol gets made non-interactive.

## Transcript
The full record of everything exchanged during a protocol run — every message, every challenge, every commitment. This is precisely what Fiat-Shamir hashes to generate challenges, and it's exactly what a zero-knowledge simulator must be able to fake convincingly (connecting back to your Predicate Logic notes on the zero-knowledge definition's quantifier structure — the simulator produces a fake transcript indistinguishable from a real one).

## Challenge-Response Protocols
The general interactive pattern underlying sigma protocols and most ZK constructions: prover sends a commitment, verifier sends a random challenge, prover responds based on both the challenge and their witness. This three-move pattern (commit-challenge-respond) is the template Fiat-Shamir transforms into a single non-interactive message, and it's the structural skeleton underneath most of the "interactive proof" material from your Discrete Math / Interactive Proofs notes.

---

## Quick self-check before moving to Cryptographic Commitments
You're ready to move on once you can, without notes:
1. Explain the hierarchy of computational, statistical, and perfect security, and give one ZK-relevant example of each (or note if one doesn't commonly appear in your work)
2. Explain precisely what the Fiat-Shamir heuristic does, and why it's the reason SNARKs can be non-interactive
3. Explain the difference between collision resistance, preimage resistance, and second-preimage resistance — precisely, not just "they're all about hash security"
4. Explain why "provably secure" always means "secure relative to an assumption," and name one computational assumption you've already encountered
