# STARKs (DEEP)

**Sources:** Continue with [STARK-101](https://starkware.co/stark-101/) (already worked through in Week 1) and the original STARK paper, Ben-Sasson, Bentov, Horesh & Riabzev, [*Scalable, Transparent, and Post-Quantum Secure Computational Integrity*](https://eprint.iacr.org/2018/046) (free, IACR ePrint, already on your reading list). Revisit both now with your implementation experience behind you; concepts that felt abstract on first read should land differently now.

---

## STARK Architecture — the full pipeline, restated precisely
Computation → **execution trace** (a table of values over time/steps) → encode trace validity as **AIR** (transition + boundary constraints) → combine constraints into a **composition polynomial** → commit to trace and composition data via **Merkle trees** → prove the committed data is **low-degree** (genuinely close to a valid polynomial, not just claimed to be) via **FRI** → verifier spot-checks a small random sample and is convinced with overwhelming probability.

## Transparent Proofs
No trusted setup — reinforcing from your Commitments notes, STARKs achieve this because their security rests on **hash function collision resistance** (Merkle trees) rather than a structured, secret-dependent SRS. This is STARK's headline advantage over KZG-based SNARKs, at the cost of larger proof sizes.

## Execution Traces
The table of values representing every step of the computation being proven — one row per execution step, one column per "register" or state variable being tracked. This is the direct analog of R1CS's witness vector, but organized as a 2D table over time rather than a flat vector — closer in spirit to PLONKish arithmetization's table model than to R1CS's matrix framing.

## AIR (Algebraic Intermediate Representation)
The overall framework encoding "what makes this trace valid" as **polynomial constraints** — the STARK equivalent of R1CS/QAP, but built around trace-table structure instead of a flat constraint-matrix structure.

## Transition Constraints
Constraints checking that **consecutive rows** of the trace are related correctly — e.g., "the value in this register at step i+1 must equal some function of the values at step i." This encodes the actual step-by-step logic of the computation being proven.

## Boundary Constraints
Constraints checking **specific fixed values at specific fixed positions** — e.g., "the trace's first row must equal the claimed public input," or "the last row must equal the claimed output." Transition constraints handle the general step-to-step logic; boundary constraints pin down the specific start/end values that matter for the actual statement being proven.

## Constraint Polynomials
The polynomials directly encoding transition and boundary constraints — each constraint, once expressed algebraically, becomes (or contributes to) a polynomial that should vanish wherever the constraint is satisfied.

## Composition Polynomial — worth being precise about this term specifically
The **single combined polynomial** built by taking a random linear combination of all the individual constraint polynomials (transition constraints, boundary constraints, all combined together) — exactly the same "combine several checks via random linear combination" trick you've now seen repeatedly (Schwartz-Zippel, PLONK's quotient polynomial). The composition polynomial is what actually gets tested for low-degree-ness via FRI — not each constraint polynomial individually, but this one combined object. Worth distinguishing clearly from "constraint polynomial" (the individual pieces) versus "composition polynomial" (the combined whole) — these terms get used loosely sometimes, but the distinction matters for precise reading.

## Vanishing Polynomial (recap)
Already fully established — for STARK's typical roots-of-unity domain, this has the efficient X^n−1 form from your Polynomial Math and FFT notes, used to divide out the "should be zero here" points from the composition polynomial.

## Trace Polynomials
The polynomials obtained by interpolating each column of the execution trace (treating the trace's row-index as the evaluation point) — directly the same interpolation process as your Polynomial Math/Basis notes, applied specifically to trace data. Each register/column of the trace becomes one trace polynomial.

## Low-Degree Testing
The general problem FRI solves: given (claimed) evaluations of a polynomial, verify they genuinely come from a polynomial of the claimed (low) degree, without needing to see all the evaluations or the polynomial itself directly. This is the core soundness mechanism protecting the whole STARK — a cheating prover who tries to use a function that *isn't* actually a low-degree polynomial gets caught by this test with overwhelming probability, connecting directly to your Coding Theory and Concentration Bounds notes.

## Polynomial Commitments, Merkle Commitments (recap)
Already established — STARKs commit to trace and composition polynomial evaluations via **Merkle trees** specifically (a vector commitment, from your Cryptographic Commitments notes), rather than KZG or IPA — this is precisely the "hash-based, transparent" choice that gives STARKs their trusted-setup-free, plausibly-post-quantum security profile.

## FRI (recap)
Already deeply built — the specific low-degree testing protocol used, based on Reed-Solomon proximity testing (Coding Theory) and domain-halving folding rounds (connecting to your Recurrences notes on the O(log n) round structure).

---

## The precision worth walking away with
The terms that are easiest to blur, now disambiguated: **AIR** is the overall framework/approach; **transition/boundary constraints** are the individual rules; **constraint polynomials** are those rules made algebraic; **composition polynomial** is all of them combined via random linear combination into one object; **FRI** is what actually gets run against that one combined object to check it's genuinely low-degree. If you can narrate this chain precisely, you understand the architecture at the level of precision papers expect, not just "STARKs use traces and FRI."

---

## Quick self-check before moving to AIR (dedicated section) / Coding Theory
You're ready to move on once you can, without notes:
1. Explain the precise difference between a transition constraint and a boundary constraint, with an example of each
2. Explain the difference between "constraint polynomial" and "composition polynomial," and what technique combines the former into the latter
3. Explain why STARKs use Merkle commitments rather than KZG/IPA, and what security property this choice is optimizing for
4. Narrate the full pipeline from execution trace to verified proof in one paragraph, from memory
