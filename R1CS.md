# R1CS ⭐⭐⭐⭐⭐ — ZK Context Notes

**You've effectively already built most of this understanding across Linear Algebra, Matrices, Matrix Multiplication, and Arithmetic Circuits notes.** This section is genuinely more consolidation than new material — treat it that way rather than re-learning from scratch.

**Sources:**
1. **Continue with your GGPR13/Groth16 paper work** — R1CS is the substrate those papers assume.
2. **Vitalik Buterin's "Quadratic Arithmetic Programs: from Zero to Hero"** — the standard, widely-cited walkthrough of R1CS→QAP conversion using a concrete worked example. This is specifically the resource to use for the one genuinely new item on this list (R1CS→QAP mechanics) — most people's "it finally clicked" moment for this conversion comes from this exact post.

---

## Rank-1 Constraint Systems — the naming, explained
Worth knowing *why* it's called "rank-1": each individual constraint is a product of exactly **two linear combinations** — (A_row·z) × (B_row·z) = (C_row·z) — and a single product of two linear forms is, in the relevant sense, a "rank-1" bilinear expression (as opposed to a general quadratic form, which could mix terms more richly). The name is describing the specific, restricted structural shape every constraint is required to take.

## Witness Vectors, Constraint Matrices, A/B/C, Linear Combinations, Multiplication Constraints, Public/Private Variables, Constraint Satisfaction
All of these are fully established from your Linear Algebra and Matrices notes — z=(1,x,w), the three m×n matrices, (A·z)⊙(B·z)=(C·z), and the free-addition/costly-multiplication distinction from Arithmetic Circuits. Rather than re-explain, use this as your checkpoint: **if any of these feel less than automatic, that's the specific earlier note to revisit**, not something new to learn here.

## R1CS Construction — the genuinely new practical piece
The actual process of **compiling a program or computation into R1CS**. The general recipe:
1. **Flatten** the computation into a sequence of statements, each containing **at most one multiplication** (this is the key constraint — anything with more than one multiplication per statement needs to be broken into multiple intermediate steps, each getting its own wire variable).
2. **Assign a wire variable** to every intermediate value produced along the way — this becomes part of your witness vector.
3. For each multiplication in the flattened sequence, **write one row** into A, B, and C encoding that specific constraint (which linear combination of variables forms the left multiplicand, which forms the right, which forms the product).
4. Additions get folded directly into the linear combinations (A_row, B_row, C_row) rather than getting their own rows, per your Arithmetic Circuits notes.

This is exactly what a Circom compiler (or any R1CS-targeting circuit compiler) does automatically — worth mentally tracing through a small example (e.g., compiling x³+x+5=out) by hand once, since doing it manually even for a tiny example makes the whole process concrete in a way reading about it doesn't.

## R1CS Optimization — practical, tool-level
Real compilers apply passes to reduce constraint count before finalizing an R1CS: **eliminating redundant constraints** (linearly dependent rows, from your Linear Algebra notes), **common subexpression elimination** (reusing a wire instead of recomputing an identical intermediate value), and **constraint merging** where structurally possible. This is genuinely comparable to what a traditional compiler's optimization passes do for regular code — except here, every constraint saved directly reduces prover time and proof size, giving circuit optimization a much higher payoff than typical software optimization.

## R1CS → QAP — the conversion you're already mid-way through
Reinforcing what you've built via the GGPR13/Groth16 papers: this conversion takes each **column** of the A, B, C matrices (one column per variable, containing that variable's coefficient across every constraint row) and **interpolates it into a polynomial** — using the constraint indices (1, 2, ..., m) as evaluation points, via Lagrange interpolation (your Polynomial Math notes). This transforms "m separate row-by-row constraint checks" into a **single polynomial identity check**: A(X)·B(X) − C(X) must be divisible by the vanishing polynomial t(X) (zero at every constraint-index point) — exactly the QAP satisfiability equation from your earlier notes, now understood as literally *built from* the R1CS matrices via this column-wise interpolation process.

---

## Quick self-check before moving to QAP (revisited) / Groth16
You're ready to move on once you can, without notes:
1. Explain the "at most one multiplication per statement" rule in R1CS construction, and why this forces intermediate wire variables to be introduced
2. Manually sketch (on paper) the R1CS matrices for a tiny example like proving knowledge of x such that x³+x+5=35
3. Explain precisely how the R1CS→QAP conversion works: what gets interpolated, using which points, into what
4. Explain why "rank-1" describes the structural restriction on each constraint (product of two linear forms), not something about the matrix's linear-algebra rank in the usual sense
