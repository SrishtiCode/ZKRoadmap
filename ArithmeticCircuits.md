# Arithmetic Circuits (SOLID -> DEEP)

**Sources (practical, matching this section's hands-on nature):**

1. [Circom documentation](https://docs.circom.io/) (docs.circom.io, free). Since you already know Circom, revisit it specifically through this lens: it's literally how circuits get built and optimized in practice, with real syntax mapping directly onto the concepts below.

2. [0xPARC's ZK Learning Resources](https://learn.0xparc.org/materials/circom/) (free), especially their Circom workshops. Well-regarded, practitioner-authored material covering common circuit patterns, under-constrained circuit bugs, and custom gate usage. Closer to "how real circuit engineers actually think" than a textbook treatment. Pair it with their [zk-bug-tracker](https://github.com/0xPARC/zk-bug-tracker) (free, community-maintained) for real-world examples of what goes wrong when these patterns aren't followed.

This section is closer to your existing practical strength (you've already built real circuit-adjacent tooling) than a new theoretical area — treat this as consolidating and sharpening vocabulary more than learning from scratch.

---

## Boolean Circuits
Circuits with AND/OR/NOT gates, wires carrying {0,1} — already covered in your Propositional Logic notes. Mentioned here mainly as the contrast case for arithmetic circuits below.

## Arithmetic Circuits — the actual object your R1CS/QAP work is built on
The generalization: wires carry **field elements** (not just {0,1}), and gates are **addition and multiplication over 𝔽_p**. This is the real underlying structure beneath everything you've built — R1CS is a specific way of encoding constraints over an arithmetic circuit, not something separate from it.

## Addition Gates — essentially free
A genuinely important practical fact: in R1CS (and most constraint systems), **addition doesn't need its own constraint row** — it's absorbed directly into the linear combinations that feed into multiplication gates. A sum of many terms costs nothing extra in constraint count; only multiplication is "expensive." This single fact shapes how real circuits get designed — engineers actively restructure computations to minimize multiplications, since additions are close to costless.

## Multiplication Gates — the actual cost unit
Since addition is free, **multiplication gates are the real unit of circuit "cost."** Each multiplication gate corresponds to exactly one R1CS constraint row. This is precisely why circuit size in ZK contexts is almost always reported as **multiplication gate count** (or R1CS constraint count) specifically, not total gate count — when someone says "this circuit has 10,000 constraints," they mean 10,000 multiplication gates, with an arbitrary amount of free addition wrapped around them.

## Fan-in
The number of inputs to a gate. Worth a precise nuance here: a single R1CS multiplication constraint is structurally "fan-in 2" at the multiplication level (it checks one linear-combination-of-wires times another linear-combination-of-wires), but **each of those two linear combinations can have arbitrarily large fan-in**, since they're built from free additions. So "fan-in 2" only describes the multiplication itself, not the full expressive reach of a single constraint row.

## Circuit Depth
The longest path from inputs to outputs (from your Graph Theory notes). Practically relevant for: how many sequential "layers" of computation exist (affects parallelization potential during witness generation) and, in some interactive-proof-based systems, the number of communication rounds needed.

## Circuit Size
Total gate count — in practice, almost always meaning multiplication gate count specifically (per the note above). This is the primary driver of both prover time and proof size in most systems, which is why "reduce circuit size" is the single most common circuit-optimization goal.

## Constraint Systems
The general umbrella term: R1CS, PLONK's custom gate constraints, and AIR's transition constraints are all **different concrete encodings** of the same underlying idea — "what makes a witness valid for this computation." Recognizing these as siblings (different encoding choices for the same fundamental concept) rather than unrelated formalisms is a useful unifying frame as you move between SNARK families this month.

## Arithmetic Constraints
The actual polynomial equations encoding gate behavior. A multiplication gate becomes a constraint like x·y=z; a boolean-forcing constraint (common for encoding bits) is b·(b−1)=0 — forcing b to be exactly 0 or 1, since that's the only way the product is zero (directly connects to your Propositional Logic notes on encoding boolean values arithmetically).

## Witnesses (recap, with an important precision)
Worth being precise here: the "witness" in a full R1CS system means **every intermediate wire value in the circuit**, not just the original private input. If a circuit computes several intermediate values on the way to its output, all of those intermediate values are part of the witness vector too — this is why witness generation (computing all these intermediate values from the initial inputs) is itself a real computational step the prover performs before proving anything.

## Public Inputs / Private Inputs
The split that becomes z=(1, x, w) in R1CS — public inputs are known to both prover and verifier, private inputs (witness proper) are known only to the prover. The distinction determines exactly what the verifier is allowed to check independently versus what must be proven without revealing.

## Circuit Optimization
Real, practical techniques for reducing constraint count: eliminating **redundant constraints** (directly connecting to your Linear Algebra notes on linearly dependent rows — a redundant constraint is one that's implied by others and can be safely removed), **common subexpression elimination** (computing a repeated sub-computation once and reusing the wire, rather than recomputing), and restructuring computation to minimize multiplications specifically (since additions are free). This is real, hands-on engineering work, not abstract theory — and it's exactly the kind of work a "Prover Engineering" (§36) mindset applies at the circuit-design level rather than the low-level-arithmetic level.

## Custom Gates — the major modern optimization technique
Instead of restricting yourself to only basic add/multiply gates, **PLONK and Halo2 let you define specialized gates for common operation patterns** — e.g., a single custom gate implementing one full round of a hash function's internal operations, rather than decomposing it into many separate basic multiplication gates. This can reduce constraint count dramatically for circuits with repeated structured operations (hash functions, range checks). This is a direct, practical payoff of your upcoming PLONK/Halo2 deep-dive (§18–20) — custom gates are one of the biggest reasons PLONK-family systems can be so much more efficient in practice than a naive R1CS encoding of the same computation.

---

## Quick self-check before moving to R1CS
You're ready to move on once you can, without notes:
1. Explain why addition gates are "free" in R1CS but multiplication gates aren't, and what this implies about how real circuits get optimized
2. Explain the fan-in nuance: why a single R1CS constraint can involve many wires even though it's structurally "fan-in 2" at the multiplication level
3. Explain what circuit "size" almost always actually means in practice, and why
4. Explain what a custom gate is and why it can dramatically reduce constraint count compared to basic R1CS encoding
