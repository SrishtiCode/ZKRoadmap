# Pairings (DEEP)

This is the mathematical machinery underneath the single pairing check you already implemented in your Groth16 verifier: the goal by the end of this section is to actually understand what that check is doing, not just call the library function.

**Best source:** Craig Costello, [*Pairings for Beginners*](https://www.craigcostello.com.au/s/PairingsForBeginners.pdf) (free). This is *the* primary source for exactly this list, covering bilinear maps through Miller's algorithm, embedding degree, optimal Ate pairing, and BN/BLS curve families in essentially this exact order. Read it start to finish for this section.

**Supplement for real depth:** Ben Lynn, [*On the Implementation of Pairing-Based Cryptography*](https://crypto.stanford.edu/pbc/thesis.html) (PhD thesis, Stanford, free). A classic, thorough treatment of Miller's algorithm and pairing computation, written by one of the BLS curve system's co-inventors (the "L" in BLS). This is where to go once Costello's intuition is solid and you want implementation-level rigor.

**Already-flagged supplement:** Sean Bowe, [*BLS12-381: New zk-SNARK Elliptic Curve Construction*](https://electriccoin.co/blog/new-snark-curve/) (free). Revisit this now specifically for the embedding-degree and optimal-Ate context, since you'll understand it much better having done the EC section first.

---

## Bilinear Maps
A function e: G₁ × G₂ → G_T satisfying **e(aP, bQ) = e(P,Q)^(ab)** for scalars a, b and points P∈G₁, Q∈G₂. This single equation is the entire reason pairings are cryptographically useful: it lets you **"multiply in the exponent"** — take values hidden inside group elements (via scalar multiplication) and verify a multiplicative relationship between them, without ever revealing the actual scalar values a and b.

## Bilinearity
The precise algebraic property defining the map: **linear in each argument separately** — e(P₁+P₂, Q) = e(P₁,Q)·e(P₂,Q), and e(P, Q₁+Q₂) = e(P,Q₁)·e(P,Q₂). From these two linearity properties, the combined e(aP,bQ)=e(P,Q)^(ab) relationship follows directly. This is the property your Groth16 pairing check exploits: the verification equation is constructed so that if (and essentially only if) the prover's hidden values satisfy the required polynomial relationship, the pairing equation balances.

## Non-degeneracy
e(P,Q) ≠ 1 whenever P and Q are both non-identity (generator) elements — the pairing isn't the trivial "always outputs 1" map, which would be cryptographically useless. Combined with bilinearity, non-degeneracy is what makes the pairing an actual **test** you can use: a meaningful, nontrivial multiplicative relationship gets reflected faithfully in G_T, rather than collapsing to a constant regardless of input.

## Pairing Groups: G₁, G₂, G_T
Three distinct groups, each playing a different role:

### G₁
Typically the "cheaper" group — points on the curve itself, over the base field. Most values you're committing to or working with directly (e.g., KZG commitments) live here, since operations in G₁ are computationally cheapest.

### G₂
Typically defined over a **field extension** (via the curve's **twist**, a related curve construction that lets you work with smaller coordinate representations than you'd otherwise need) — points here have coordinates in an extension field, making G₂ operations meaningfully more expensive than G₁ operations. This directly connects to your Field Extensions/Tower Extensions notes from Abstract Algebra.

### G_T
The **target group** of the pairing — lives inside the full extension field 𝔽_{p^k}*, where k is the embedding degree (below). This is the most expensive of the three groups to compute in, which is exactly why **final exponentiation and the Miller loop being expensive matters so much for real prover performance** — pairing computation is disproportionately costly precisely because it necessarily touches this large extension field.

## Pairing-Friendly Curves
**Not every elliptic curve supports efficient pairings.** For a pairing to be both computable and secure, the curve's **embedding degree** (below) needs to be simultaneously: small enough that G_T arithmetic stays tractable, and large enough that the discrete log problem in G_T remains hard. Most "random" curves fail this balance badly (embedding degree is typically either enormous, making G_T computation infeasible, or the curve simply doesn't admit a useful pairing at all) — this is exactly why specific curve families (BN, BLS) were **deliberately constructed**, via careful polynomial parametrization, to hit a good embedding degree. Pairing-friendliness is a rare, engineered property, not a generic curve feature.

## Embedding Degree
The smallest integer k such that G_T is contained in 𝔽_{p^k}* — equivalently, the degree of field extension needed to "hold" the pairing's target group. This single number is doing enormous practical work: it **directly determines the cost of G_T arithmetic** (larger k → more expensive field operations) and **the security level** (k needs to be large enough that DLOG in the resulting extension field is hard). **BLS12-381's "12" is exactly this embedding degree** — the curve is specifically constructed so k=12 hits the right balance of efficiency and security for a 381-bit base field. This is precisely why your Tower Extensions notes matter here: computing directly in 𝔽_{p^12} is expensive, so real implementations decompose it as a tower (𝔽_p → 𝔽_{p²} → 𝔽_{p⁶} → 𝔽_{p^12}) for efficiency.

## Miller's Algorithm / Miller Loop
The actual algorithm that computes the pairing function. Core idea: it evaluates specific **rational functions** associated with the points, accumulated across a loop structurally similar to double-and-add scalar multiplication (from your EC notes) — hence "Miller loop." This loop is the primary computational cost of evaluating a pairing, and essentially every pairing-related optimization (including the Optimal Ate construction below) is fundamentally about making this loop shorter or cheaper per iteration.

## Final Exponentiation
After the Miller loop completes, the raw result must be raised to a specific power — **(p^k − 1)/r** (where r is the prime subgroup order) — to map it into a canonical, well-defined representative within G_T. Without this step, the Miller loop's raw output depends on exactly *which* representative of an equivalence class was used for the inputs, which would make the pairing ill-defined as a cryptographic primitive. This is a **separate, also computationally significant** step, distinct from and performed after the Miller loop — real pairing implementations spend meaningful time on both phases, and optimizing final exponentiation specifically is its own active area of implementation work.

## Optimal Ate Pairing
The specific, most computationally efficient pairing construction used in essentially all modern implementations (including BLS12-381), as opposed to older, less-optimized constructions (the original Weil or Tate pairings). "Optimal" here refers to achieving the theoretically shortest possible Miller loop length for a given curve family, exploiting specific structural properties of BN/BLS-style curves. When a paper or library says "we compute a pairing," in practice for a modern ZK system it almost always means the Optimal Ate pairing specifically, not a generic pairing construction.

## BN Curves
The Barreto-Naehrig family: curves parametrized by a single integer x, with the prime p, the group order r, and the trace t all expressed as **specific polynomials in x** — a deliberate construction technique that lets curve designers search for good parameters systematically rather than by trial and error over arbitrary curves. Designed specifically to achieve embedding degree 12 with controllable curve size. BN254 (from your Elliptic Curves notes) is this family's most historically prominent member.

## BLS Curves
The Barreto-Lynn-Scott family — a related but distinct polynomial-parametrization construction technique, generally offering **better computational efficiency** than BN curves for comparable security levels, which is why BLS12-381 has become the preferred modern choice over BN254. The "12" in both "BN254...→ well, BN doesn't use 12 in its name, but conceptually" and "BLS12-381" refers to the **embedding degree** — this naming convention (family name + embedding degree + bit-size) is standard across pairing-friendly curve literature, worth recognizing on sight.

---

## Quick self-check before moving to Zero-Knowledge Fundamentals
You're ready to move on once you can, without notes:
1. Explain what bilinearity means precisely, and why e(aP,bQ)=e(P,Q)^(ab) is the property that makes pairings cryptographically useful
2. Explain what embedding degree is, why it can't be too small or too large, and what BLS12-381's "12" refers to
3. Explain, at a high level, what the Miller loop computes and why final exponentiation is a separate, necessary step afterward
4. Explain why G₂ operations are more expensive than G₁ operations, connecting this to field extensions
