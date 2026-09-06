# Number Theory — ZK Context Notes

**Best source:** **"A Computational Introduction to Number Theory and Algebra" by Victor Shoup** — free, full text online (shoup.net/ntb). This is an unusually good fit for you specifically: it's written by one of the top applied cryptographers (co-author of Boneh-Shoup), has a genuinely *computational/algorithmic* focus rather than pure theory, and covers every single item on your list — integers through primality testing — in the order and depth a cryptography-focused reader needs. This is your primary source; you likely won't need a second one for this section.

---

## Integers
The starting point before reduction. Almost everything in ZK eventually gets reduced modulo a prime p, but understanding integer arithmetic properly (division with remainder, the well-ordering principle) is the base layer beneath modular arithmetic — worth being explicit that "integers" and "field elements" are related but distinct: field elements are equivalence classes of integers under mod-p congruence.

## Divisibility
Foundational vocabulary (a | b means a divides b) used to define GCD, primality, and modular inverses. Also shows up directly in **vanishing polynomials**: t(X) "divides" A(X)B(X) − C(X) is the core QAP satisfiability check — the same divisibility concept, just applied to polynomials instead of integers (polynomial rings behave a lot like ℤ in this respect, which is worth noticing).

## Prime Numbers
**The field modulus p must be prime** for 𝔽_p to actually be a field (if p weren't prime, some nonzero elements wouldn't have multiplicative inverses, breaking division). Every curve you use (BLS12-381, BN254) is defined over a prime field, and the curve's group order is also chosen with specific primality properties for security and efficiency (e.g., having a large prime-order subgroup to avoid small-subgroup attacks).

## GCD
Used to check coprimality — e.g., verifying an exponent is coprime to the group order (relevant to some scheme parameter choices) — and as the algorithmic backbone of computing modular inverses (below).

## Extended Euclidean Algorithm
**Computes modular inverses directly** — given a and n with gcd(a,n)=1, it finds x such that a·x ≡ 1 (mod n). This is one of two standard ways to compute a field element's multiplicative inverse (the other being Fermat's Little Theorem, below) — you use this every single time you divide field elements, which is constant in polynomial evaluation, FFT, and Lagrange interpolation.

## Modular Arithmetic
This **is** the arithmetic your entire system runs on. Every field operation (addition, multiplication) in 𝔽_p is integer arithmetic followed by reduction mod p. Efficient modular reduction (Montgomery reduction, Barrett reduction) is a real prover-engineering concern — naive mod operations are slow, and production ZK libraries implement optimized reduction algorithms.

## Congruences
The notation a ≡ b (mod n) — "a and b have the same remainder mod n" — is the basic language every modular arithmetic statement is written in. Fluent reading of congruence notation is a prerequisite for reading Fermat/Euler's theorems below without friction.

## Modular Inverse
Field **division** is defined as multiplication by the modular inverse: a/b = a · b⁻¹ (mod p). This is used constantly — FFT requires dividing by n during the inverse transform, Lagrange interpolation requires dividing by products of differences, KZG evaluation proofs require polynomial division. If your modular inverse computation is slow or wrong, everything built on top breaks.

## Fermat's Little Theorem
States: for prime p and a not divisible by p, **a^(p−1) ≡ 1 (mod p)**. Two direct practical uses: (1) it gives a fast way to compute modular inverses — a⁻¹ ≡ a^(p−2) (mod p), computable via fast exponentiation (square-and-multiply) — often preferred over Extended Euclidean in ZK libraries because it parallelizes/vectorizes better; (2) it's the basis of the simplest primality test (Fermat test), useful background even though production systems use stronger tests (Miller-Rabin).

## Euler's Theorem
The generalization of Fermat's Little Theorem to **composite** moduli: a^φ(n) ≡ 1 (mod n) when gcd(a,n)=1, where φ is Euler's totient function. Less directly used in prime-field ZK arithmetic day-to-day (since most ZK fields are prime, where Fermat's theorem already applies directly), but it's the correct general theorem and relevant if you ever touch RSA-adjacent constructions or composite-order group schemes.

## Euler Phi Function (φ)
Counts the size of the multiplicative group mod n. For a prime p, φ(p) = p−1, which is exactly the order of the multiplicative group 𝔽_p* — relevant whenever you need to reason about the group order (e.g., when finding generators, or reasoning about the size of the challenge space for soundness-error calculations).

## Chinese Remainder Theorem (CRT)
Lets you split a computation modulo a large composite number into independent computations modulo its prime-power factors, then recombine. Real, direct application: **NTT (Number Theoretic Transform) and some polynomial evaluation strategies use CRT-based decomposition** to split work across smaller moduli, useful for optimization. Also conceptually relevant to some batch-verification techniques that combine multiple independent checks into one via CRT-style combination.

## Quadratic Residues
An element a is a quadratic residue mod p if some x exists with x² ≡ a (mod p) — i.e., a has a square root. **Directly relevant to elliptic curve point decompression**: given a curve equation y² = x³ + ax + b, finding y for a given x requires computing a square root mod p, which requires knowing whether the right-hand side is a quadratic residue at all (if it isn't, that x doesn't correspond to a valid curve point).

## Legendre Symbol
A compact notation/algorithm for determining whether a is a quadratic residue mod p (returns +1, −1, or 0). This is the computational tool behind the **Tonelli-Shanks algorithm**, which is what real ECC libraries use to actually compute square roots mod p — needed for point decompression and certain curve operations you'll encounter once you're deep in elliptic curve implementation work.

## Discrete Logarithm
**This is the actual hardness assumption underlying most of the pairing-based cryptography you're using.** Given g and gˣ in a group, finding x is believed computationally infeasible (for well-chosen groups) — this is the Discrete Logarithm Problem (DLOG), and variants of it (like q-SDH, used specifically in Groth16's security proof) are the hard problems that soundness reductions reduce to. When you read "assuming DLOG is hard" in a paper, this is the exact concept.

## Computational Number Theory
The algorithmic/efficiency side of everything above: fast modular exponentiation (square-and-multiply, getting O(log n) multiplications instead of O(n)), efficient modular reduction (Montgomery/Barrett), and efficient GCD computation. This is squarely **prover engineering** (§36) territory — the theory tells you *what* to compute, this tells you how to compute it fast, which is the actual differentiator in production prover performance.

## Prime Generation
Needed when choosing a field modulus or verifying a curve's parameters are correctly chosen. Real systems use **probabilistic primality tests** (Miller-Rabin being the standard) rather than deterministic factorization, since they're vastly faster for large numbers while having negligible (and tunable) error probability — connecting directly back to the "negligible probability" concept from your Probability notes.

---

## Quick self-check before moving to Abstract Algebra
You're ready to move on once you can, without notes:
1. Explain why the field modulus p must be prime, and what breaks if it isn't
2. State two different ways to compute a modular inverse, and why one might be preferred over the other in a real implementation
3. Explain what the Discrete Logarithm Problem is, and name one ZK construction whose security directly depends on it being hard
4. Explain why quadratic residues matter for elliptic curve point decompression
