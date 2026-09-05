# Discrete Mathematics — ZK Context Notes

Read alongside: MIT 6.042 "Mathematics for Computer Science" (Lehman/Leighton/Meyer, free PDF)

---

## Sets, Functions, Relations

**Sets**
- A finite field 𝔽_p is a *set* {0, 1, ..., p-1} with two operations defined on it. Every "evaluation domain" in FFT/polynomial work is a specific set (usually a multiplicative subgroup of a field).
- Notation to get comfortable with: ∈ (element of), ⊆ (subset), |S| (cardinality — you'll see this constantly for field size, domain size).

**Functions**
- Key properties to know cold: **injective** (one-to-one), **surjective** (onto), **bijective** (both — a permutation).
- Hash functions are modeled as functions with specific properties (collision-resistant, preimage-resistant).
- A **permutation** = a bijective function from a set to itself. This is the exact object behind PLONK's permutation argument.

**Relations**
- An NP relation R is a set of pairs (x, w) — statement and witness. "x ∈ L" (x is in the language) means ∃w such that (x,w) ∈ R.
- **This is the formal definition of what a SNARK proves knowledge of.** When Groth16's paper says "argument for relation R," this is exactly what's meant.

---

## Logic

**Propositional logic**
- AND/OR/NOT gates in an arithmetic/boolean circuit = propositional logic connectives.
- Circuit satisfiability = "does there exist an assignment making this propositional formula true?" — this is the NP-complete problem your entire R1CS/QAP pipeline reduces to.

**Predicate logic**
- Quantifiers ∀ (for all) and ∃ (there exists) show up in every security definition you'll read:
  - Completeness: "∀ valid (x,w), Pr[Verify(x, Prove(x,w)) = 1] = 1"
  - Soundness: "∀ PPT adversaries A, ∀ x ∉ L, Pr[A convinces V] ≤ negl(λ)"
- Get comfortable parsing these on sight — this is the actual notation of every paper from Groth16 onward.

---

## Proof Techniques

**Direct proof**
- Used for completeness proofs: assume honest prover + honest inputs, mechanically show the verification equation holds. Straightforward, algebraic.

**Proof by contradiction**
- **This is the backbone of every soundness proof in ZK.** Structure: "Assume a cheating prover P* exists that convinces the verifier with non-negligible probability on a false statement. We show how to use P* to break some underlying hard problem (e.g., compute a discrete log). Since that problem is assumed hard, no such P* exists."
- This pattern is called a **reduction**. Learn to recognize "reduction to a hard problem" as contradiction's specific application in crypto.

**Proof by induction**
- Structural backbone of recursive proof systems: "if the accumulated state is valid at step n, and one more step is verified correctly, it's valid at step n+1." This is literally how Incrementally Verifiable Computation (IVC) and folding schemes (Nova) are proven correct.
- Also used in circuit-size complexity proofs (e.g., inductive arguments about constraint count as circuit depth grows).

**Pigeonhole principle**
- Underlies collision-resistance intuition: hash more inputs than output-space size, and collisions must exist somewhere. Relevant background for reasoning about Merkle tree / FRI commitment security bounds.

---

## Combinatorics

**Permutations**
- **Direct, load-bearing usage:** PLONK's copy constraints check that "this wire value equals that wire value elsewhere in the circuit" via a **permutation check** — verified efficiently using a grand product argument over the permutation. You cannot understand PLONK/Halo2 circuit wiring without this.

**Combinations**
- Used in soundness-error calculations: e.g., "the verifier picks a random challenge from a set of size q; a cheating prover succeeds only if they guessed correctly, probability ~1/q." Understanding how challenge-space size (a combinatorial quantity) determines security level is a recurring calculation.

**Graph theory**
- A circuit *is* a directed acyclic graph: gates = nodes, wires = edges. **Circuit depth** and **circuit width** — both graph properties — directly determine proving cost and are terms you'll see in every circuit-optimization discussion.
- Merkle trees (used in FRI, STARK commitments) are literally trees — tree depth = log(number of leaves) directly determines Merkle proof size, which directly determines your STARK proof size.

**Recurrences**
- FFT's O(n log n) complexity comes from solving the recurrence T(n) = 2T(n/2) + O(n) (Master Theorem). If you want to *derive* rather than just accept why FFT beats naive O(n²) polynomial multiplication, this is the tool.
- Also shows up in analyzing recursive proof composition cost (how proof size/verification time grows, or doesn't, across recursion levels).

---

## Quick self-check before moving on
You're ready to move to Number Theory (Day 1's next block) once you can, without notes:
1. Explain what an NP relation is and connect it to what a SNARK proves
2. Explain why soundness proofs use contradiction/reduction, with the general shape of the argument
3. State what a permutation argument is checking and why it matters for PLONK
4. Solve (or recognize the solution to) T(n) = 2T(n/2) + O(n)

If any of these feel shaky, that's the specific sub-topic to revisit — not a signal to re-read the whole section.
