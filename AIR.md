# AIR (IMP) — ZK Context Notes

**Heavy overlap with your STARKs notes — most of this list is reinforcement. Two genuinely new items deserve real attention: DEEP-ALI and DEEP-FRI.**

**Sources: continue STARK-101 + the original STARK paper** for everything already familiar. **For DEEP-ALI/DEEP-FRI specifically: the "DEEP-FRI" paper** (Ben-Sasson, Goldberg, Kopparty, Saraf) — this is the actual paper introducing the technique, worth reading directly since it's a meaningful soundness improvement over "vanilla" FRI that your original STARK-101 pass may not have covered in depth.

---

## Execution Trace, Trace Table, Trace Columns (recap)
Fully established from your STARKs notes — the table of computation state over time, one column per register/variable, one row per step. "Trace table" and "execution trace" are used interchangeably; "trace columns" specifically refers to the individual registers being tracked.

## Transition Constraints, Boundary Constraints (recap)
Fully established — step-to-step rules vs. fixed-position rules, respectively.

## Initial Constraints, Final Constraints — the two boundary-constraint subtypes, named precisely
Worth being precise: **initial constraints** are boundary constraints applied specifically at the **first row** of the trace (typically pinning the trace to the claimed input); **final constraints** are boundary constraints applied at the **last row** (typically pinning the trace to the claimed output). These are the two most common special cases of "boundary constraint" you'll actually write in practice — most AIR designs need exactly these two, rather than boundary constraints scattered at arbitrary intermediate positions.

## Algebraic Constraints
The general term for any constraint expressed as a polynomial equation that must vanish — covering transition, boundary, initial, and final constraints all under one umbrella term. When a paper says "AIR consists of a set of algebraic constraints," this is the category being referenced.

## Constraint Degree — a real, practical parameter
The **degree of the polynomial expressing a given constraint** — this matters practically because it directly affects the **blow-up factor** needed (how much larger your evaluation domain needs to be than your trace length, connecting to your Cosets/FFT notes on domain extension) and the size of the composition polynomial. Higher-degree constraints (e.g., encoding a complex operation like a full hash round in one transition constraint) require a correspondingly larger evaluation domain to maintain soundness — this is a genuine circuit/AIR design tradeoff: fewer, higher-degree constraints per row versus more, lower-degree constraints spread across more rows.

## Composition Polynomial (recap)
Fully established from your STARKs notes — the random-linear-combination of all constraint polynomials into one object.

## Quotient Polynomial — AIR's version
Directly analogous to QAP's h(X): after building the composition polynomial, you **divide by the vanishing polynomial** (below) to get a quotient — and this division having **zero remainder** is exactly the check confirming all constraints are satisfied everywhere they should be. Same underlying Polynomial Divisibility concept from your Polynomial Math notes, now in the AIR/STARK context specifically rather than the QAP/Groth16 context.

## Vanishing Polynomial (recap)
Fully established — for the roots-of-unity trace domain, this has the efficient X^n−1 closed form.

---

## DEEP-ALI — the genuinely new technique
**ALI** stands for **Algebraic Linking IOP** — the general technique for linking trace polynomials to the constraint/composition polynomial via evaluation checks. **DEEP** (Domain Extension for Eliminating Pretenders) is a refinement addressing a real soundness subtlety: in the basic construction, there's a gap where a cheating prover *could* exploit specific algebraic relationships between the trace and composition polynomials to pass verification without a fully valid trace. DEEP-ALI closes this gap by having the prover additionally evaluate their polynomials at a **randomly chosen out-of-domain point** (a point outside the original trace evaluation domain) and prove consistency there too — this extra out-of-domain check is specifically what "eliminates pretenders" (cheating provers who'd otherwise slip through the basic construction). Worth understanding this as: **DEEP-ALI = the standard ALI linking technique, plus one crucial extra randomized check that closes a soundness gap.**

## DEEP-FRI — FRI combined with the DEEP technique
The practical combination: applying the DEEP out-of-domain-point technique **together with FRI's low-degree testing**, giving meaningfully **better soundness bounds** than using FRI alone without the DEEP enhancement. This is what most modern, production STARK implementations actually use rather than "vanilla" FRI as originally described — if you're reading a recent STARK system's documentation and it mentions "DEEP-FRI," this is precisely the enhanced construction being referenced, and it's worth treating as the practical default rather than a niche variant.

---

## Quick self-check before moving to Coding Theory
You're ready to move on once you can, without notes:
1. Explain the difference between initial and final constraints, and why these are the two most common boundary-constraint cases in practice
2. Explain what constraint degree affects practically, connecting it to blow-up factor and domain size
3. Explain, at a conceptual level, what soundness gap DEEP-ALI's out-of-domain point check is closing, and why an ordinary in-domain check wouldn't catch it
4. Explain why DEEP-FRI is generally preferred over vanilla FRI in production systems
