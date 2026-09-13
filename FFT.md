# FFT / NTT (DEEP)

**Sources (two, complementary):**

1. **Continue with von zur Gathen & Gerhard's** [*Modern Computer Algebra*](https://cosec.bit.uni-bonn.de/science/mca/) for the rigorous algorithmic treatment (Cooley-Tukey, radix-2 structure, complexity analysis).

2. Vitalik Buterin, [*Fast Fourier Transforms*](https://vitalik.ca/general/2019/05/12/fft.html) (free blog post). Written specifically for a STARK/ZK audience, genuinely excellent intuition-building. This is the one most ZK developers actually cite as "the post that made FFT click." Read this *before* or alongside the formal treatment, not after, it'll make the formal version land faster.

**Optional deep supplement (finite-field/performance side):** [Ingonyama's blog](https://www.ingonyama.com/post) has NTT-focused technical posts written by ZK hardware/prover engineers, including their free book [*Foundations of NTT Hardware Design*](https://github.com/ingonyama-zk/papers/blob/main/ntt_201_book.pdf). Genuinely useful once you want to go past "I understand FFT" into "I understand why NTT implementations are engineered the way they are," relevant to your Prover Engineering (§36) goals later.

---

## Roots of Unity
An n-th root of unity is a field element ω such that ωⁿ = 1. These are the special evaluation points FFT is built around — instead of evaluating a polynomial at arbitrary points, you evaluate at the n-th roots of unity specifically, because their algebraic structure (closure under multiplication, symmetric powers) is exactly what makes the divide-and-conquer FFT algorithm work.

## Primitive Roots
An n-th root of unity ω is **primitive** if its order is exactly n — not a smaller divisor of n. You need a *primitive* n-th root to generate the full set of n distinct roots (ω, ω², ω³, ..., ωⁿ=1) without repeats. If you accidentally used a non-primitive root, you'd only reach a smaller subgroup, missing points in your intended evaluation domain.

## Evaluation Domains
The actual set of points {1, ω, ω², ..., ω^(n-1)} used for the evaluation-form representation of your polynomials. This connects directly to your Basis and Polynomial Math notes — choosing this specific domain (rather than arbitrary points) is exactly what unlocks FFT's efficiency for basis conversion.

## Multiplicative Subgroups
The n-th roots of unity form a **cyclic subgroup** of the field's multiplicative group 𝔽_p* — directly connecting back to your Abstract Algebra notes (cyclic groups, generators, subgroups). ω is the generator of this specific subgroup. Recognizing "evaluation domain" and "cyclic multiplicative subgroup" as the same object is what lets group theory and FFT efficiency reasoning reinforce each other rather than feeling like separate topics.

## DFT (Discrete Fourier Transform)
The general operation: given a polynomial's coefficients, compute its values at a chosen set of points (originally roots of unity over the complex numbers, generalized to finite fields for ZK use). Naively, computing all n evaluations costs O(n²) (evaluate the degree-(n-1) polynomial at each of n points independently). FFT is the fast algorithm for computing exactly this operation.

## FFT
The fast, O(n log n) divide-and-conquer algorithm for computing the DFT — splitting a size-n problem into two size-n/2 subproblems (even-indexed and odd-indexed coefficients), recursively solving each, then combining. This is precisely the recurrence T(n) = 2T(n/2) + O(n) from your Discrete Math notes, now attached to a concrete algorithm.

## Inverse FFT (IFFT)
Converts evaluation-form back to coefficient-form — the reverse basis change. Computationally very similar to forward FFT, but requires **dividing by n** at the end, which means n must be invertible in your field (i.e., n and p must be coprime — true for any n < p in a prime field, but this requirement is exactly why "FFT-friendly" field choice matters, see below).

## NTT (Number Theoretic Transform)
**FFT specialized to work over a finite field instead of the complex numbers.** Since every ZK system operates in 𝔽_p, this — not the classical complex-number FFT — is the actual algorithm real provers implement. The name changes (NTT instead of FFT) but the algorithmic structure (Cooley-Tukey, radix-2 splitting) is identical; the only real difference is that "roots of unity" now means roots of unity *within the finite field*, which requires the field to actually contain enough of them (see "FFT over finite fields" below). In casual ZK conversation, "FFT" and "NTT" are often used interchangeably even though NTT is technically the correct term for what's happening.

## Radix-2 FFT
The specific, most common FFT variant that splits the problem by even/odd coefficient index at each recursive step, requiring the transform size n to be a power of 2. This constraint (n must be a power of 2) is exactly why ZK systems often pad circuits/traces up to the next power of 2 — it's not arbitrary, it's what makes radix-2 FFT/NTT applicable at all.

## Cooley-Tukey
The classic algorithm/formula implementing radix-2 FFT's recursive combine step — when people say "the FFT algorithm" without further qualification, this is usually the specific algorithm meant. Worth knowing the name since papers and library code frequently reference "Cooley-Tukey" directly.

## Cosets — used for extending evaluation domains
A coset shifts the evaluation domain: instead of evaluating at {1, ω, ω², ...}, you evaluate at {g, gω, gω², ...} for some shift element g. **Real, practical use in STARKs**: to construct the "low-degree extension" (evaluating a trace polynomial over a *larger* domain than its natural degree requires, the "blow-up factor" in FRI), a coset of the original domain is used — this lets you extend the evaluation domain efficiently using the same FFT machinery, rather than needing an entirely different evaluation strategy. If you've read about "blow-up factor" in your own STARK prover work, this coset construction is the mechanism underneath it.

## Bit Reversal
An index-reordering quirk of how in-place radix-2 FFT implementations naturally organize their output (or input): the natural recursive splitting produces results in "bit-reversed" order relative to the input index. Real FFT/NTT implementations include an explicit bit-reversal permutation step to correct this ordering. This is a genuine implementation detail worth knowing exists so you're not confused when you see a `bit_reverse` function in FFT library code — it's not a bug or an optional step, it's structurally necessary given how the recursion naturally lays out results.

## FFT over Finite Fields — why "FFT-friendly primes" are chosen deliberately
For NTT to work, your field 𝔽_p must actually **contain** a primitive n-th root of unity for the n you need — this requires n to divide (p−1) (the order of the multiplicative group). Fields are often chosen specifically so that p−1 has a large power of 2 as a factor (high "2-adicity"), guaranteeing large power-of-2-sized NTT domains are available. This is exactly why certain primes get chosen deliberately for ZK systems — e.g., the Goldilocks field (used in Plonky2) and BLS12-381's scalar field are both selected partly for favorable 2-adicity, making large NTTs efficient. When you see a field described as "FFT-friendly," this 2-adicity property is what's being referenced.

## Polynomial Evaluation using FFT
The forward direction: given a polynomial in coefficient form, use FFT to compute its values at all n roots-of-unity points in O(n log n), instead of O(n²) naive evaluation-per-point. This is how a prover efficiently moves from "I have these coefficients" to "I have these evaluation-form values" whenever both representations are needed.

## Polynomial Interpolation using FFT
The reverse direction: given a polynomial's values at the n roots-of-unity points, use IFFT to recover its coefficients in O(n log n) — vastly cheaper than general Lagrange interpolation's O(n²) for arbitrary points. This efficiency gain is *specifically* a consequence of using roots-of-unity as your points rather than arbitrary ones — it's the payoff for constraining your evaluation domain to have this special algebraic structure in the first place.

---

## Quick self-check before moving to Computational Complexity
You're ready to move on once you can, without notes:
1. Explain the difference between a root of unity and a *primitive* root of unity, and why the distinction matters for building an evaluation domain
2. Explain why NTT, not classical complex-number FFT, is what real ZK provers implement
3. Explain why circuit/trace sizes often get padded to a power of 2, connecting this to radix-2 FFT's requirements
4. Explain what "FFT-friendly prime" means, in terms of 2-adicity and root-of-unity availability
