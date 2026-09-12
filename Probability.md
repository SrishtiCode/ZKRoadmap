# Probability (SOLID)

**Best source:** Mitzenmacher & Upfal, [*Probability and Computing*](https://www.cambridge.org/core/books/probability-and-computing/3A5B47DB315FC64B9256C5C8131C5EFA) (Cambridge University Press, not free, check your library or university access). The standard CS-oriented probability text; it covers union bound, concentration inequalities (Chernoff/Hoeffding), and the birthday paradox explicitly and rigorously, exactly the subset of probability theory that shows up in cryptography, as opposed to a general stats textbook that would spend most of its time on things you won't use.

**Supplement (short, crypto-specific):** the probability primer/appendix in Boneh & Shoup, [*A Graduate Course in Applied Cryptography*](https://toc.cryptobook.us/) (already on your reading list, free full text). It defines **statistical distance** and **negligible probability** precisely, in the exact notation crypto papers use, worth reading even if you've covered general probability elsewhere, since the crypto-specific formalization is what you'll actually see in papers.

---

## Probability Spaces
Every ZK security definition is a statement about probability over some **randomness source**: the verifier's random challenges, the Fiat-Shamir hash output treated as random, the prover's random blinding factors. The "probability space" is formally the set of all possible random coin flips a party could receive, with a probability measure over it. When a paper writes "Pr[...]", it's implicitly quantifying over this space.

## Random Variables
A random variable is a function *of* the randomness. Example: "the verifier's challenge c" is a random variable — a function from the probability space (the verifier's random coins) to a value in the challenge set. "Whether the adversary wins the security game" is also a random variable (0 or 1, depending on the randomness). Get comfortable reading Pr[X = 1] style statements as "the probability this random variable takes value 1."

## Conditional Probability
Used constantly in security game analysis: "Pr[adversary succeeds | adversary queried the oracle on input y]" — reasoning about probability *given* some event or adversary behavior. Hybrid-argument proofs (common in crypto security proofs, where you show a sequence of games are indistinguishable) lean heavily on conditional probability reasoning.

## Independence
A core modeling assumption: verifier challenges across different rounds are assumed **independent** (in the random oracle model, each new hash query is treated as an independent fresh random value). This independence assumption is what lets you multiply probabilities across rounds to get overall soundness error — if challenges weren't independent, an adversary could potentially exploit correlations to cheat more easily than the stated soundness bound suggests.

## Expected Value
Used for average-case analysis: expected number of FRI queries needed, expected proving time over random inputs, expected number of attempts before an adversary succeeds by luck (connects directly to soundness-error-as-inverse-of-expected-tries reasoning).

## Variance
Less central to core security proofs, but genuinely useful in **prover engineering** (§36) — when benchmarking your prover, understanding variance in proving time across runs (not just the average) tells you whether performance is stable or whether some inputs trigger worst-case behavior worth investigating.

## Distributions
The **uniform distribution** is the default assumption almost everywhere in ZK: challenges are drawn uniformly at random from a field or challenge set, field elements in an SRS are treated as if uniformly sampled during setup. Recognizing when a paper assumes uniformity (and what breaks if that assumption is violated — e.g., a biased challenge distribution can leak information or reduce soundness) is a genuinely important reading skill.

## Birthday Paradox — directly determines your security parameters
The birthday paradox says: among a group of just ~√N people, a shared birthday (collision) becomes likely, even though N (365) seems large. Applied to hash functions: for an n-bit hash output, a collision becomes likely after roughly **2^(n/2)** queries, not 2ⁿ. **This is exactly why a 256-bit hash function is said to have only "128-bit collision resistance"** — the birthday bound halves the effective security level for collision-finding attacks (though not for preimage attacks, which stay at ~2ⁿ). This directly explains parameter choices you'll see everywhere (why SHA-256 vs. SHA-512 get chosen for different security targets).

## Union Bound
States: Pr[A or B or C or ...] ≤ Pr[A] + Pr[B] + Pr[C] + ... — even if the events aren't independent. This is the workhorse tool for combining multiple sources of failure probability into one bound. Used constantly in ZK soundness proofs: "the probability the adversary succeeds via strategy 1, OR strategy 2, OR ... is at most the sum of each individual probability" — lets you bound total soundness error across many possible attack vectors without needing to reason about their interactions.

## Concentration Bounds
Bounds like **Chernoff** and **Hoeffding** tell you that a random sample's behavior stays tightly clustered around its expected value with high probability. **This is the actual mathematical machinery behind FRI's query-based soundness**: the verifier only checks a random subset of positions (doesn't examine the whole committed function), and concentration bounds are what justify that this random sample reliably reflects whether the whole function is "close to" a low-degree polynomial or not — with overwhelming probability, a small random sample can't be fooled into missing a large deviation.

## Statistical Distance
A precise measure of how distinguishable two probability distributions are: SD(D₁, D₂) = ½ Σ|Pr[D₁=x] - Pr[D�2=x]|, ranging from 0 (identical distributions) to 1 (fully distinguishable). This is what makes "zero-knowledge" a precise mathematical claim rather than a vague intuition: **statistical zero-knowledge** means the real transcript and the simulated transcript have statistical distance 0 (or negligibly close to 0) — genuinely indistinguishable, even to an unbounded adversary. This is stronger than *computational* zero-knowledge, which only requires indistinguishability against efficient (polynomial-time) adversaries.

## Negligible Probability — the single most important term on this list
Formally: a function ε(λ) is **negligible** if for every polynomial p(λ), eventually ε(λ) < 1/p(λ) — it shrinks faster than the inverse of any polynomial as the security parameter λ grows. This exact term appears in literally every soundness/security definition you'll read ("...except with negligible probability"). It's the formalization of "so small it doesn't matter in practice, even against a powerful adversary" — as opposed to just "small," which isn't precise enough for a security proof. Get this definition exact; it's the load-bearing word in almost every theorem statement in the field.

---

## Quick self-check before moving to Number Theory
You're ready to move on once you can, without notes:
1. Explain why a 256-bit hash function only offers ~128-bit collision resistance, using the birthday paradox
2. State the union bound and explain how it's used to combine multiple soundness-error sources into one total bound
3. Explain, in plain terms, why concentration bounds are what let FRI's verifier check only a small random sample instead of the whole committed function
4. Give the precise definition of "negligible probability" (not just "very small") — what does it mean relative to *every* polynomial?
