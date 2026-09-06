# Linear Algebra — ZK Context Notes

**Best source:** Gilbert Strang's **"Introduction to Linear Algebra"** + his free MIT OCW 18.06 lecture series (video lectures on YouTube, lecture notes free on MIT OCW). This is the standard recommendation for exactly your use case — intuition-first, computationally grounded, widely regarded as the best on-ramp for programmers/engineers rather than pure math majors. It covers every sub-topic in your list. You don't need a second source for this section; if you want a supplementary "seen through a crypto lens" resource, revisit these notes after Strang rather than searching for a ZK-specific linear algebra text (they're rare and usually thinner than just applying Strang's material directly).

---

## Scalars
In ZK, a scalar is almost always a **field element** — a member of 𝔽_p. Every coefficient in a polynomial, every entry in a witness vector, every challenge value from Fiat-Shamir is a scalar in some finite field. Get comfortable with the idea that "scalar" here doesn't mean "real number" — it means "element of whatever field the whole system is defined over," usually 𝔽_p for a large prime p, or an extension field.

## Vectors
The **witness vector z** in R1CS (z = (1, x, w) — constant, public input, private witness concatenated) is literally a vector over 𝔽_p. Coefficient lists of polynomials are vectors. Understanding vector operations (addition, scalar multiplication) directly transfers to how witnesses and polynomial representations are manipulated.

## Matrices
The **A, B, C matrices in R1CS** are the central object — each row encodes one constraint, each column corresponds to one variable in z. A constraint system with m constraints and n variables is exactly a set of three m×n matrices. Fluency reading "the R1CS matrices" as literal matrices (not just abstract "constraint tables") makes every SNARK paper's notation click faster.

## Matrix Multiplication
R1CS satisfaction is checked as **(A·z) ⊙ (B·z) = (C·z)** — matrix-vector multiplication producing three vectors, then elementwise (Hadamard) multiplication compared against the third. This single line is the entire R1CS definition; you should be able to compute it by hand for a small example.

## Linear Combinations
A linear combination (sum of scalar-weighted vectors) is exactly what **MSM (multi-scalar multiplication)** computes — a·G₁ + b·G₂ + ... over elliptic curve points. This is the single most expensive operation in most provers (KZG commitments, Groth16 proof elements are all linear combinations of SRS points). Understanding "linear combination" deeply is understanding what your prover spends most of its time computing.

## Span
The span of a set of vectors is everything reachable via their linear combinations. Relevant conceptually: the SRS (structured reference string) in KZG/Groth16 is a specific set of curve points, and what a prover can *legitimately* construct is constrained to the span of those points — this is part of why the pairing check works to catch malformed proofs (things outside the expected span/structure fail verification).

## Basis
Two bases matter constantly in ZK: the **monomial basis** (1, X, X², X³, ...) and the **Lagrange basis** (polynomials that are 1 at one evaluation point and 0 at all others). Converting between coefficient-form (monomial basis) and evaluation-form (Lagrange basis) *is* what FFT/IFFT does. QAP construction relies explicitly on Lagrange interpolation — building u(X), v(X), w(X) from constraint points using the Lagrange basis.

## Dimension
The dimension of your witness space = the number of variables (n) in your R1CS. The dimension of your constraint space = number of constraints (m). Polynomial degree is a dimension concept too — a degree-d polynomial lives in a (d+1)-dimensional vector space of polynomials.

## Linear Independence
Constraint rows should generally be linearly independent for a well-formed, non-redundant system — a linearly *dependent* row adds no new constraint (it's implied by others), which can hide the true "effective" constraint count. This also connects to why **random linear combinations** are used so heavily in ZK protocols (e.g., "prove these two vectors are equal by checking a random linear combination"): a nonzero vector will only vanish under a random linear combination with negligible probability — the core trick behind polynomial identity testing (Schwartz-Zippel) that half of every SNARK's soundness proof relies on.

## Rank
The rank of the R1CS matrices bounds the "true" degrees of freedom in the constraint system — closely related to circuit complexity. Rank arguments also appear directly in soundness proofs: showing a matrix/relation has full rank is often how you prove there's essentially only one valid witness (up to the intended freedom) satisfying a set of constraints.

## Null Space — genuinely important for security, not just theory
The null space of a matrix M is {v | M·v = 0} — all vectors that get mapped to zero. **This is directly connected to under-constrained circuit vulnerabilities**, the single most common real-world ZK bug class flagged earlier in your roadmap. If a constraint matrix has a nontrivial null space larger than intended, it means there exist *multiple different witnesses* satisfying the same constraints — some of which may be malicious/invalid inputs that a careless circuit designer didn't intend to allow. Recognizing "does this constraint system have unintended null-space freedom" is a real skill security-minded ZK developers use.

## Linear Transformations
Every R1CS matrix is literally a **linear transformation** — a map from the witness vector space to the constraint output space, satisfying T(u+v) = T(u)+T(v) and T(cu) = cT(u). Many commitment schemes are also linear (or "additively homomorphic"): KZG commitments satisfy Commit(f+g) = Commit(f) + Commit(g), which is a linear-transformation property that enables batch verification and proof aggregation tricks.

## Gaussian Elimination
Used practically in witness generation (solving a system of linear equations to determine intermediate wire values from inputs) and in circuit optimization/simplification (detecting and eliminating redundant constraints, which connects back to linear independence above).

## Eigenvalues & Eigenvectors
Less directly central day-to-day, but genuinely useful for a deep understanding of FFT: the **roots of unity used as the FFT evaluation domain are eigenvalues of the cyclic shift operator** on the vector space of sequences. If you want to understand *why* FFT's specific choice of evaluation points (roots of unity) is not arbitrary but structurally necessary, this eigenvalue connection is the rigorous "why." Treat this as depth-building rather than daily-use knowledge.

## Vector Spaces
The unifying concept underneath almost everything above: polynomials of degree < d form a vector space; the set of valid codewords in a Reed-Solomon code (relevant to FRI/coding theory) forms a vector space; the witness space itself is a vector space over 𝔽_p. When a paper says "the space of degree-d polynomials," it's invoking this concept directly — you should recognize vector space structure (closure under addition and scalar multiplication) wherever it appears rather than treating each instance as a new idea.

## Dual Spaces
Appears in two places worth knowing: (1) **Coding theory** — every linear code has a "dual code," and dual-code properties are used in some proximity-testing and soundness arguments related to Reed-Solomon codes (connects to your Coding Theory / FRI study). (2) **Pairing structure** — the two source groups G₁ and G₂ in a pairing e: G₁×G₂→G_T have a relationship that's conceptually adjacent to duality (though technically it's a bilinear map, not a strict dual-space construction) — worth flagging now, and you'll see the precise relationship once you're deep in Pairings (§10). Treat this topic as SKIM-level for now; revisit if it resurfaces in a specific proof you're reading.

---

## Quick self-check before moving to Probability
You're ready to move on once you can, without notes:
1. Write out what (A·z)⊙(B·z) = (C·z) means, dimensionally — what size is each matrix, what size is z, what size is the result
2. Explain why a nontrivial null space in a constraint matrix is a security concern, not just a linear-algebra curiosity
3. Explain the connection between "linear combination" and MSM, and why MSM dominates prover cost
4. State the difference between the monomial basis and the Lagrange basis, and name the operation that converts between them
