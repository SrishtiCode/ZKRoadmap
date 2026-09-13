# QAP (DEEP)

This section is mainly about nailing the precise notation, since that's usually the actual sticking point with QAP, not the underlying idea.

**Sources:** Continue with Gennaro, Gentry, Parno & Raykova, [*Quadratic Span Programs and Succinct NIZKs without PCPs*](https://eprint.iacr.org/2012/215) (GGPR13, free, IACR ePrint) and Vitalik Buterin's [*Quadratic Arithmetic Programs: from Zero to Hero*](https://medium.com/@VitalikButerin/quadratic-arithmetic-programs-from-zero-to-hero-f6d558cea649) (already recommended for R1CS → QAP). No new sources needed, this is the same material viewed at full resolution.

---

## Quadratic Arithmetic Programs — the core idea, restated precisely
A QAP re-expresses R1CS's per-row matrix check as a **single polynomial identity**. Where R1CS had m separate scalar equations (one per constraint row), QAP compresses all of them into one equation over polynomials — checkable at a single random point instead of m separate places, which is exactly the efficiency gain that makes SNARKs succinct.

## Constraint Points
The m indices (1, 2, ..., m — or any fixed set of m distinct field elements) used as the **evaluation points** for interpolation. Each constraint point corresponds to exactly one R1CS row. These are literally the roots of your vanishing polynomial t(X), tying directly back to your Factor Theorem notes.

## Lagrange Interpolation (recap)
The tool used to build each uᵢ(X), vᵢ(X), wᵢ(X) below — already fully covered in your Polynomial Math notes.

## uᵢ(X), vᵢ(X), wᵢ(X) — the notation that trips people up
This is worth being precise about, since the subscripts are doing something specific: for **each variable i** (each column of the R1CS matrices), you get one uᵢ(X), one vᵢ(X), one wᵢ(X) — built by interpolating that variable's column of coefficients from matrix A (giving uᵢ), from matrix B (giving vᵢ), and from matrix C (giving wᵢ), across the m constraint points. So uᵢ(X) is "how much variable i contributes to the A-side of each constraint, as a polynomial in the constraint index." There's one full family {u₁,...,uₙ}, {v₁,...,vₙ}, {w₁,...,wₙ} — one polynomial per variable per matrix.

## A(X), B(X), C(X) — combining with the witness
These are built by taking a **linear combination of the uᵢ (resp. vᵢ, wᵢ) polynomials, weighted by the actual witness values**: A(X) = Σᵢ zᵢ·uᵢ(X), and similarly B(X) = Σᵢ zᵢ·vᵢ(X), C(X) = Σᵢ zᵢ·wᵢ(X). This is the step where your specific witness z gets folded in — uᵢ/vᵢ/wᵢ encode the *circuit structure* (fixed, independent of any particular witness), while A(X)/B(X)/C(X) encode *this specific execution* (witness-dependent). This distinction — structural polynomials vs. witness-combined polynomials — is exactly the kind of precision that makes QAP notation click instead of blur together.

## Target Polynomial / Vanishing Polynomial / t(X)
All the same object — reinforcing from your Polynomial Math and Factor Theorem notes: t(X) = (X−a₁)(X−a₂)...(X−aₘ), zero exactly at the m constraint points. "Target polynomial" and "vanishing polynomial" are used interchangeably in the literature; both refer to t(X).

## Quotient Polynomial / h(X)
Defined by the QAP satisfiability equation itself: h(X) = (A(X)·B(X) − C(X)) / t(X). **This only exists as an actual polynomial (rather than a rational function with nonzero remainder) if the witness genuinely satisfies every constraint** — this is precisely the Polynomial Divisibility check from your Polynomial Math notes, now applied as the central QAP correctness criterion. Computing h(X) is real, concrete work the prover performs — and it's exactly what your Groth16 proof-generation implementation from Day 1 involves.

## Polynomial Divisibility (recap)
Already covered — the general "does t(X) divide (A(X)B(X)−C(X)) with zero remainder" check, either via full division or via the more efficient random-point evaluation trick from your Polynomial Identities notes.

## QAP Satisfiability
The full condition, stated precisely: a witness z satisfies the QAP if and only if **A(X)·B(X) − C(X) = h(X)·t(X)** for some polynomial h(X) — equivalently, t(X) divides (A(X)B(X)−C(X)) exactly. This single equation is the entire target Groth16's construction is designed to let a prover convince a verifier of, without revealing z.

## R1CS → QAP (recap)
Already covered in your R1CS notes — the column-wise interpolation process that builds uᵢ/vᵢ/wᵢ from the R1CS matrices. Worth reinforcing here that this happens **once per circuit** (independent of any specific witness) — it's part of the circuit's fixed structure, computed during setup, not something redone for every proof.

---

## The full picture, notation made precise
Circuit (fixed) → R1CS matrices A,B,C (fixed) → interpolate columns → **uᵢ(X), vᵢ(X), wᵢ(X)** (fixed, one triple per variable, independent of witness) → combine with **this specific witness z** → **A(X), B(X), C(X)** (witness-dependent, one triple total) → check **A(X)B(X)−C(X) = h(X)t(X)** → if true, witness is valid, and h(X) is the concrete polynomial the prover computes and commits to as (part of) the proof.

---

## Quick self-check before moving to Groth16 (revisited)
You're ready to move on once you can, without notes:
1. Explain the precise difference between uᵢ(X) (per-variable, structural) and A(X) (witness-combined) — why does this distinction matter?
2. Explain what h(X) is, how it's computed, and why it only exists as a true polynomial when the witness is valid
3. Write out the full QAP satisfiability equation from memory, and explain each term's role
4. Explain why R1CS→QAP interpolation happens once per circuit rather than once per proof
