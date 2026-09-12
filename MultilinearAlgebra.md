# Multilinear Algebra — ZK Context Notes

**This completes your second originally-flagged priority gap (paired with Sumcheck). Heavy overlap with your Sumcheck notes for the first three items — the genuinely new material is tensor products and multilinear commitment schemes, both worth real attention since they're the "how do you actually implement this efficiently" layer underneath sumcheck-based systems.**

**Best source: continue Thaler's book** — it has dedicated, thorough treatment of multilinear commitment schemes specifically, in addition to the sumcheck/MLE material you've already drawn from it. This remains the right single source for this entire cluster.

---

## Multilinear Polynomials, Multilinear Extensions, Boolean Hypercube (recap)
Fully established in your Sumcheck notes — degree ≤1 per variable, the unique extension of hypercube-defined data to a full polynomial, and the 2ⁿ-point domain these constructions are built around. No new ground here; this section builds directly on top.

## Tensor Products — the algebraic structure making everything efficient
A genuinely new, important piece: many of the key objects in multilinear-polynomial land have a **tensor product structure** that makes them efficiently computable despite involving exponentially many (2ⁿ) terms. The clearest example: the **equality polynomial** eq(x,r) = Πᵢ(xᵢrᵢ + (1−xᵢ)(1−rᵢ)) — a multilinear polynomial that equals 1 when x=r (both in {0,1}ⁿ) and 0 for every other hypercube point — factors as a **product across each variable independently**. This tensor structure is exactly what lets you compute all 2ⁿ hypercube-weighted evaluation terms **efficiently** (via a table-doubling approach, below) rather than needing to naively evaluate an exponential-size formula term by term. Tensor products also show up directly in how **multilinear commitment schemes structure their setup data** — a multilinear KZG-style SRS, for instance, is organized as a tensor product of per-variable commitment components, mirroring the same "one factor per variable" structure as the equality polynomial itself.

## Hypercube Evaluations — the efficient algorithms, not just the concept
Worth distinguishing from the abstract MLE concept: this is specifically about **how you actually compute** an MLE's evaluation at an arbitrary point, or a sum over the whole hypercube, **efficiently**. The standard approach is a **table-doubling dynamic-programming algorithm**: rather than naively recomputing each of the 2ⁿ hypercube-weighted terms independently (which would cost O(n·2ⁿ)), you build up a table of partial products incrementally, doubling its size with each new variable processed, achieving the full computation in **O(2ⁿ)** total work — linear in the number of hypercube points, not quadratic-ish. This efficiency is directly what makes sumcheck's prover practical: each round of sumcheck requires exactly this kind of hypercube-summation work, and a naive implementation would make sumcheck far too slow to be useful despite being theoretically elegant.

## Multilinear Commitments — the commitment-scheme family parallel to §8, now for MLEs specifically
This is the other genuinely new piece: KZG, IPA, and FRI-based commitments (your §8 notes) were all built around **univariate** polynomials. Sumcheck-based systems (Spartan, HyperPlonk, GKR-based constructions) work with **multilinear** polynomials instead — and committing to *those* efficiently requires a **parallel family of commitment schemes** specifically adapted for the multilinear setting:

- **Multilinear KZG** — extends KZG's pairing-based trick to multiple variables, using the tensor-product SRS structure mentioned above. Same trusted-setup tradeoff as regular KZG, now generalized.
- **Hyrax** — based on Pedersen commitments (your Cryptographic Commitments notes) combined with the tensor structure of multilinear polynomials, achieving a transparent (no trusted setup) multilinear commitment scheme — a different tradeoff point than multilinear KZG.
- **Multilinear extensions of FRI-based schemes** (e.g., Basefold, Brakedown-style constructions) — bringing the transparent, hash-based, plausibly-post-quantum properties of FRI (your §24 notes) into the multilinear setting.

**Worth recognizing this as a genuinely parallel structure to what you already learned**: just as univariate polynomial commitments split into KZG (pairing-based, trusted setup) / IPA (transparent, discrete-log) / FRI (transparent, hash-based) tradeoffs, multilinear commitments split along essentially the same axes — multilinear KZG, Hyrax, and FRI-derived multilinear schemes occupy the same three tradeoff positions, just adapted for multilinear rather than univariate polynomials.

## Sumcheck (recap)
Fully established — this whole document exists to support the efficient machinery underneath sumcheck-based proving, which you already understand at the protocol level.

---

## The full picture, both flagged gaps now closed
You started this month with two explicitly flagged gaps: **Sumcheck** and **Multilinear Algebra**. Together, they give you: the protocol (sumcheck's round reduction), the mathematical objects it operates on (multilinear polynomials, MLEs, the Boolean hypercube), the algebraic structure making it efficient (tensor products, hypercube-evaluation algorithms), and the commitment-scheme layer needed to actually build a real SNARK from it (multilinear commitments). This is a complete, closed loop — you could, in principle, now read a Spartan or HyperPlonk paper and follow every piece of its construction, rather than treating any of it as a black box.

---

## Quick self-check — closing out this whole cluster
You're ready to move on once you can, without notes:
1. Explain the tensor product structure of the equality polynomial eq(x,r), and why this structure is what makes efficient hypercube computation possible
2. Explain, at a high level, how the table-doubling algorithm achieves O(2ⁿ) hypercube evaluation instead of a naive, more expensive approach
3. Name the three main multilinear commitment scheme families and explain how each maps onto the KZG/IPA/FRI tradeoff axes you already understand from univariate commitments
4. Explain why sumcheck-based systems need a fundamentally different commitment scheme family than PLONK/STARK systems, connecting this back to the univariate vs. multilinear distinction
