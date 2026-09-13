# Cryptographic Commitments (DEEP)

**Sources (two, matching the two halves of this topic):**

1. Boneh & Shoup, [*A Graduate Course in Applied Cryptography*](https://toc.cryptobook.us/) (continuing your spine reference, free) for the general commitment scheme definitions: hiding, binding, and their computational/statistical/perfect variants, with the precise game-based definitions.

2. Justin Thaler, [*Proofs, Arguments, and Zero-Knowledge*](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf) (free). This book has a dedicated chapter comparing KZG, IPA (Bulletproofs-style), and FRI-based commitments side by side, exactly the comparative view this section needs. Since Thaler is already your Tier-1 spine textbook, this is the natural source rather than hunting down three separate original papers.

**If you want the original sources for depth later:**
- Kate, Zaverucha & Goldberg, [*Polynomial Commitments*](https://cacr.uwaterloo.ca/techreports/2010/cacr2010-10.pdf) (2010, free technical report, University of Waterloo CACR), the original KZG paper.
- Bünz, Bootle, Boneh, Poelstra, Wuille & Maxwell, [*Bulletproofs: Short Proofs for Confidential Transactions and More*](https://eprint.iacr.org/2017/1066) (free, IACR ePrint), for IPA.

Thaler's comparative treatment is sufficient for a first strong pass.

---

## Commitment Schemes (general)
A two-phase primitive: **commit** (the committer binds themselves to a value without revealing it) and **open** (later reveal the value, with a proof that it matches the original commitment). Every polynomial commitment scheme you use is a specific instantiation of this general pattern, applied to polynomials instead of arbitrary values.

## Hiding
The commitment reveals nothing about the committed value until opened. This is the "zero-knowledge-adjacent" property of a commitment — someone seeing your commitment shouldn't learn anything about what you committed to.

## Binding
The committer cannot later open their commitment to a *different* value than what they originally committed to — no "changing your mind" after the fact. This is the "soundness-adjacent" property — it's what makes a commitment trustworthy evidence rather than an empty promise.

## Computational Hiding / Statistical Hiding
Same hierarchy as your Crypto Foundations notes: **computational hiding** holds only against efficient adversaries; **statistical hiding** holds even against unbounded ones. Pedersen commitments (below) are statistically hiding — genuinely no information leaks, regardless of adversary power.

## Computational Binding / Perfect Binding
**Computational binding** means breaking it (finding two different values that open to the same commitment) requires solving a hard problem — this is what KZG relies on. **Perfect binding** means it's absolutely impossible, even for an unbounded adversary, to open to two different values. There's a real tradeoff here worth noting: **a commitment scheme cannot be both perfectly hiding and perfectly binding simultaneously** — you always trade one for the other, which is why every real scheme picks a specific point on this spectrum deliberately (Pedersen: perfectly/statistically hiding, computationally binding; many hash-based commitments: perfectly binding, only computationally hiding).

## Pedersen Commitments
A classic, simpler commitment scheme: Commit(v, r) = v·G + r·H, using two independent generator points G, H on an elliptic curve, with r as random blinding. **Statistically hiding, computationally binding** (breaking binding requires solving discrete log). Worth understanding as a stepping stone before KZG — it's the same "linear combination of curve points" idea from your earlier notes, just committing to a single value instead of a whole polynomial. Also genuinely useful as a building block: Pedersen commitments are additively homomorphic, same as KZG, for the same underlying reason (it's a linear combination).

## Vector Commitments
Commit to an entire vector of values, with the ability to later open individual entries **without revealing the rest of the vector**. **Merkle trees are a vector commitment scheme** — the root is the commitment, and a Merkle authentication path is the opening proof for one specific entry, binding via the underlying hash function's collision resistance (directly connecting to your Crypto Foundations notes). This reframes something you already know (Merkle trees) as an instance of a more general concept (vector commitments), which is useful — FRI/STARK commitments are, at their core, vector commitments to trace/evaluation data.

## Polynomial Commitments
The specific case central to your work: committing to an entire polynomial, then later proving specific **evaluations** without revealing the whole polynomial. This is the umbrella concept the rest of this section elaborates on.

---

## KZG
Pairing-based, requires a **trusted setup** (structured reference string, Powers of Tau). Its defining strength: **constant-size commitments and constant-size evaluation proofs**, regardless of polynomial degree — a single group element each. You've already implemented this. The opening mechanism (proving p(a)=y) is exactly the Remainder Theorem trick from your Polynomial Math notes: compute q(X)=(p(X)−y)/(X−a), commit to q(X), and the pairing check verifies this division was done correctly.

## IPA (Inner Product Argument)
**Transparent — no trusted setup required**, unlike KZG. Based on the Bulletproofs-style inner product argument, and it's the commitment scheme underlying Halo2's original (non-KZG) backend. Tradeoff versus KZG: proof size and verifier time are **O(log n)** rather than constant — you give up KZG's O(1) succinctness in exchange for removing the trusted setup requirement entirely. This is a genuine, deliberate design tradeoff you'll see debated constantly when comparing proving systems.

## FRI-based Commitments
**Also transparent** — no trusted setup — based on Reed-Solomon proximity testing (your Coding Theory / FRI work). Used in STARKs. Tradeoff profile: typically **larger proof sizes** than KZG or IPA, but transparent setup and (because it's built on hash functions rather than elliptic curve pairings) generally considered to have better **post-quantum security properties**, since discrete-log-based assumptions (which KZG relies on) are broken by quantum computers, while hash-based security isn't (at least not by the same class of attack).

**The three-way comparison worth internalizing:** KZG (trusted setup, O(1) proofs, pairing-based), IPA (transparent, O(log n) proofs, discrete-log-based), FRI (transparent, larger proofs, hash-based/plausibly post-quantum). No single scheme dominates — this is exactly why different production systems (zkSync uses KZG-adjacent approaches, Halo2 offers IPA, STARK systems use FRI) make different choices based on which tradeoff matters most for their use case.

## Commitment Opening
The general act of revealing a value (or evaluation) together with a proof that it matches an earlier commitment. Every scheme above implements this differently, but the role it plays in the protocol is identical.

## Evaluation Proofs
The specific type of opening used for polynomial commitments: proving p(a) = y for a committed polynomial p, without revealing p itself. This is the operation your KZG implementation performs, and it's the core primitive every SNARK built on polynomial commitments (PLONK, Halo2, STARKs) repeatedly invokes throughout its verification process.

## Batch Opening
Proving **multiple evaluations of the same polynomial** (e.g., p(a₁)=y₁, p(a₂)=y₂, ..., p(aₖ)=yₖ) with a single, combined proof rather than k separate ones. Real, practical efficiency gain — most production SNARKs need to open several polynomials at several points as part of one proof, and batching these opening proofs together (rather than sending k independent proofs) is a meaningful proof-size and verifier-time optimization used throughout PLONK and Halo2-family systems.

## Multi-opening
Closely related to batch opening, sometimes used interchangeably, but worth distinguishing when a paper is precise: multi-opening often refers specifically to opening **multiple different polynomials** (rather than one polynomial at multiple points) efficiently in a combined proof. The underlying trick in both cases is usually the same: use a **random linear combination** (your Schwartz-Zippel-adjacent trick from Linear Combinations) to combine several evaluation claims into one aggregate claim that can be checked with one proof.

## Commitment Aggregation
The broader idea of combining multiple separate commitments (or proofs) into a smaller aggregate — reducing what needs to be sent/verified overall. This connects to **recursive proof composition** and **proof aggregation** as concepts (your upcoming Recursive Proofs §29) — batch opening and multi-opening are specific instances of this more general aggregation idea, applied specifically to polynomial commitment openings.

---

## Quick self-check before moving to Elliptic Curves
You're ready to move on once you can, without notes:
1. Explain why no commitment scheme can be both perfectly hiding and perfectly binding at the same time
2. Compare KZG, IPA, and FRI-based commitments on: trusted setup requirement, proof size, and the type of hardness assumption each relies on
3. Explain how a batch opening proof reduces the cost of proving several evaluations, connecting it to the random-linear-combination trick from your Linear Combinations notes
4. Explain why Merkle trees can be understood as a vector commitment scheme, and what provides their binding property
