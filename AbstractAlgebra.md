# Abstract Algebra — ZK Context Notes

**Best source:** Continue with Victor Shoup, [*A Computational Introduction to Number Theory and Algebra*](https://shoup.net/ntb/ntb-v2.pdf) — same source as your Number Theory section, free full text online. It's structured to cover exactly this progression: groups → rings → fields, with a computational lens throughout. Genuinely one of the best single resources for a ZK developer specifically, since it treats algebra as something you compute with, not just something you prove theorems about. No second source needed for this section.

---

## GROUPS

### Groups
A group is a set with one binary operation satisfying closure, associativity, an identity element, and inverses. **Elliptic curve points under point addition form a group** — this is the single most important instance of "group" in your entire ZK toolkit. Every EC-based construction (KZG, Groth16, any pairing-based scheme) is built on this group structure.

### Abelian Groups
A group where the operation is commutative (a+b = b+a). EC groups are abelian — point addition order doesn't matter. This isn't just a technicality: commutativity is what makes **MSM (multi-scalar multiplication) optimizations** possible, since algorithms like Pippenger's rely on being able to reorder and batch additions freely.

### Subgroups
A subset of a group that's itself a group under the same operation. **Directly security-critical**: pairing-friendly curves have a large prime-order subgroup, and cryptographic operations must happen strictly within that subgroup. Points outside it (or in small subgroups) enable **small-subgroup attacks** — this is exactly why real implementations perform "subgroup checks" and "cofactor clearing" before trusting a curve point.

### Cyclic Groups
A group generated entirely by repeated application of one element (the generator): {g, g², g³, ..., gⁿ=identity}. The multiplicative group 𝔽_p* is cyclic, and the prime-order subgroups used in EC cryptography are cyclic. This structure is what makes discrete-log-based hardness assumptions meaningful — DLOG is hard specifically in these cyclic groups.

### Generators
The specific element g (or point G, for EC groups) that generates the whole cyclic group via repeated operation. This is a public parameter in essentially every scheme you'll implement — every scalar multiplication (b·G) you compute is expressed relative to this fixed generator.

### Group Order
|G|, the number of elements in the group. Choosing groups with the right order (typically large prime order) is a core security decision — group order factorization directly determines vulnerability to certain attacks (Pohlig-Hellman exploits groups with smooth/composite order).

### Element Order
The order of a specific element g is the smallest k such that gᵏ = identity. Relevant for subgroup checks: verifying a point has the expected (large prime) order, rather than accidentally lying in a small-order subgroup, is a real correctness/security check performed in implementations.

### Cosets
For subgroup H of G, a coset is a shifted copy {gh | h ∈ H} for some g. Conceptually underlies **cofactor clearing**: a curve's full point group often has order (prime × small cofactor), and "clearing the cofactor" (multiplying by the cofactor) is what projects an arbitrary point into the correct prime-order subgroup — a coset-structure operation, even if implementations don't always frame it that way explicitly.

### Quotient Groups
G/H — the group formed by treating each coset of H as a single element. Less directly hands-on in daily ZK work, but the *concept* of "quotienting out" unwanted structure reappears constantly, most visibly in quotient rings (below) which are far more central to your daily work.

### Homomorphisms
A structure-preserving map between groups: φ(a+b) = φ(a)+φ(b). **This is directly why commitment schemes are useful**: KZG and Pedersen commitments are **additively homomorphic** — Commit(f+g) = Commit(f) + Commit(g) — which is literally a group homomorphism property. This homomorphism is what enables batch verification, proof aggregation, and linear combination tricks used throughout PLONK/Halo2. Pairings themselves (e: G₁×G₂→G_T) are a *bilinear* map, a related but distinct structure worth not conflating with a plain homomorphism.

### Isomorphisms
A bijective homomorphism — two groups that are "the same" structurally, just labeled differently. Practically relevant: **affine and projective coordinate representations of the same elliptic curve are isomorphic** — different ways of representing the identical group, chosen for computational efficiency (projective coordinates avoid expensive field inversions during point addition). Understanding this as "same group, different representation" rather than "different math" clarifies a lot of curve-arithmetic code you'll read.

---

## RINGS

### Rings
A set with two operations (+, ×) satisfying ring axioms — crucially, **multiplicative inverses aren't required** for every element (unlike a field). ℤ is a ring. This generalization matters because...

### Polynomial Rings
**𝔽_p[X]** — the ring of polynomials with coefficients in 𝔽_p — is the actual mathematical home of nearly everything you do in STARKs and SNARKs. Every polynomial you construct (trace polynomials, constraint polynomials, QAP polynomials) lives in this ring. Polynomial addition/multiplication follow ring rules, and notably polynomials don't generally have multiplicative inverses (you can't always divide one polynomial by another and get a polynomial back) — this is exactly why "does t(X) divide A(X)B(X)−C(X)" is a meaningful, nontrivial question in QAP satisfiability, rather than something trivially always true.

### Ideals
A special subset of a ring closed under addition and absorption (multiplying an ideal element by anything in the ring stays in the ideal). Used to define quotient rings (below) — the ideal generated by a polynomial t(X) is essentially "all multiples of t(X)," and this is precisely the structure underlying "working modulo t(X)."

### Quotient Rings
**𝔽_p[X] / (t(X))** — this notation appears constantly once you're deep in QAP/polynomial-commitment territory, and it means "the polynomial ring, with everything reduced modulo t(X)," analogous to how 𝔽_p itself is ℤ reduced modulo p. This is also **exactly how field extensions are constructed**: 𝔽_{p^n} is built as 𝔽_p[X] / (irreducible polynomial of degree n) — adjoining a root of an irreducible polynomial and working modulo it. Recognizing this pattern connects your QAP work and your field-extension work as the same underlying construction.

---

## FIELDS

### Fields
A ring where *every* nonzero element has a multiplicative inverse — division always works (except by zero). This is what makes 𝔽_p suitable for the arithmetic your entire system depends on: you need to divide constantly (FFT, Lagrange interpolation, polynomial evaluation), and a field guarantees you always can.

### Finite Fields
Fields with finitely many elements — the actual arithmetic system every ZK computation runs over. 𝔽_p (prime field) is the most common you'll use directly.

### Field Characteristic
The smallest positive integer p such that adding 1 to itself p times gives 0. Prime fields 𝔽_p have characteristic p. This matters for algorithm choices: **characteristic-2 fields (binary fields) behave differently from odd-characteristic fields** in ways that affect which formulas and optimizations apply (e.g., certain curve formulas, certain hash constructions are characteristic-dependent) — worth knowing this term exists so you're not confused when a paper specifies "over a field of characteristic p."

### Field Extensions / Extension Fields
𝔽_{p^n}, built from 𝔽_p by adjoining a root of a degree-n irreducible polynomial (via the quotient-ring construction above). **Directly load-bearing for pairings**: the target group G_T in a pairing e: G₁×G₂→G_T lives in a large extension field — for BLS12-381 specifically, G_T lives in 𝔽_{p^12}. You cannot understand pairing computation (Miller loop, final exponentiation) without extension fields being solid.

### Tower Extensions
Building an extension field step by step rather than all at once: 𝔽_p → 𝔽_{p²} → 𝔽_{p⁶} → 𝔽_{p^12}, where each step adjoins a new root. **This is a genuine prover-engineering topic, not just theory**: real pairing implementations (including for BLS12-381) use tower-extension arithmetic specifically because computing directly in 𝔽_{p^12} is expensive, while breaking it into a tower of smaller extensions makes Miller loop and final exponentiation dramatically faster. This connects directly to your Pairings (§10) deep-dive.

---

## IMPORTANT ZK FIELDS

### 𝔽_p (Prime Fields)
The base field for most systems you'll touch — BN254 and BLS12-381 both have prime scalar and base fields. Most of your R1CS/QAP/KZG work happens directly in a prime field.

### 𝔽_{p^n} (Extension Fields)
Used two ways in practice: (1) pairing target groups (as above), and (2) **soundness amplification for small STARK-friendly fields** — Plonky2's Goldilocks field is only 64 bits (chosen for fast native arithmetic), which alone doesn't give enough soundness error margin, so proofs are constructed using a degree-2 extension of Goldilocks to get adequate security while keeping the base field small and fast.

### Binary Fields (𝔽_{2^n})
Fields of characteristic 2, increasingly relevant in newer STARK-friendly constructions (e.g., recent binary-field-based proving systems like Binius) — chosen because binary-field arithmetic maps very efficiently onto hardware (XOR-heavy operations, no carries). Worth knowing this direction exists even if you don't implement it this month — it's a genuinely active research/engineering frontier.

### Prime Fields (reiterated)
Worth noting explicitly as a category because most of your practical implementation work (KZG, Groth16, R1CS) is prime-field arithmetic — this is your "home base" field type.

### Extension Fields (reiterated)
Worth noting as the category you'll reach for whenever prime-field arithmetic alone isn't enough — either for pairing target groups, or for soundness amplification in small-field STARK systems.

---

## Quick self-check before moving to Polynomial Mathematics
You're ready to move on once you can, without notes:
1. Explain why elliptic curve points under addition form a group, and why that group being abelian matters practically
2. Explain what a quotient ring is, using 𝔽_p[X]/(t(X)) as your example, and connect it to QAP satisfiability
3. Explain how a field extension like 𝔽_{p^12} is constructed, and why pairings need one
4. Explain why tower extensions are used in practice rather than working directly in 𝔽_{p^12}
