# Coding Theory (DEEP)

**Best source:** Guruswami, Rudra & Sudan, [*Essential Coding Theory*](https://cse.buffalo.edu/faculty/atri/courses/coding-theory/book) (free draft, Creative Commons licensed). The standard modern reference, and notably written partly by Madhu Sudan, one of the actual pioneers connecting coding theory to the PCP theorem and proximity testing. This is a genuinely good fit for exactly what you need, rather than a generic coding theory textbook aimed at communications engineering.

**For the direct FRI connection specifically:** revisit the FRI paper (already on your list) and Thaler's book's treatment of Reed-Solomon proximity testing now, with this coding theory foundation freshly in place. Concepts that felt like "just accept this" on first read should now have real justification underneath them.

---

## Error-Correcting Codes
The general idea: encode a message with deliberate **redundancy**, so that even if some of the encoded data gets corrupted, the original message can still be recovered or the corruption detected. This is classical coding theory's core subject — originally for noisy communication channels, but the mathematical structure turns out to be exactly what ZK proximity testing needs.

## Linear Codes
Codes where the set of valid codewords forms a **vector space** — closed under addition and scalar multiplication (directly connecting to your Linear Algebra / Vector Spaces notes). Reed-Solomon codes (below) are linear codes: the sum of two valid RS codewords is itself a valid RS codeword, since it corresponds to the evaluations of the sum of two polynomials, which is itself a polynomial of at most the same degree.

## Hamming Distance
The number of positions where two strings (codewords) differ. The basic distance metric coding theory is built around — if two codewords have a small Hamming distance, they're "close"; if large, they're "far."

## Reed-Solomon Codes — the central object for everything you've built
**Codewords are the evaluations of a low-degree polynomial at a fixed set of points.** Encoding a message (the polynomial's coefficients, degree < k) means evaluating it at n > k points — the redundancy comes from having more evaluation points than the polynomial's degree requires to be uniquely determined. **This is exactly what your STARK prover's trace/composition polynomial evaluations are** — every polynomial commitment you make via evaluation-form data is, structurally, constructing a Reed-Solomon codeword.

## Evaluation Codes
The general category Reed-Solomon belongs to — codes constructed by evaluating some structured mathematical object (here, polynomials) at many points, rather than using some other encoding scheme. Worth knowing this is a category (there are other evaluation codes for other structured objects), with RS being the specific, dominant instance for ZK purposes.

## Codewords
The actual encoded strings — for RS specifically, one full codeword is "this particular polynomial's evaluations across the entire domain." When you compute your STARK trace polynomial's evaluations at every point in your FFT domain, that complete list of values is literally one Reed-Solomon codeword.

## Distance (of a code)
The **minimum** Hamming distance between any two *distinct* codewords in the code. This single number determines how much corruption the code can tolerate while still being distinguishable/correctable. **For Reed-Solomon specifically**: a code using degree-<k polynomials evaluated at n points has minimum distance exactly **n−k+1** — this is the **Singleton bound**, and Reed-Solomon codes achieve it with equality, making them **MDS (Maximum Distance Separable)** codes — provably the best possible distance achievable for a given rate. This isn't a coincidence or a nice bonus; it's exactly why Reed-Solomon codes were chosen as FRI's foundation rather than some other code family — you get the best possible error-detection capability for the redundancy you're paying for.

## Rate — the real design knob you'll actually tune
Rate = message length / codeword length = **k/n** for an RS code (degree-<k polynomials, n evaluation points). This is a genuine, practical tradeoff you'll encounter directly: **higher rate** (n close to k) means smaller domains, faster proving, but **less redundancy** — weaker distance, which translates to needing *more* FRI queries to maintain the same soundness level. **Lower rate** (n much larger than k) means more redundancy, stronger per-query soundness, but larger domains and more prover work. **The "blow-up factor" from your FFT/Cosets notes is literally 1/rate** — this connects your practical STARK-building experience directly to this abstract coding-theory parameter. Choosing blow-up factor in a real STARK implementation *is* choosing this rate tradeoff.

## Error Correction
Given a possibly-corrupted codeword, recovering the original message — not the primary use case in ZK (you're not trying to "fix" a cheating prover's data), but foundational background: algorithms like **Berlekamp-Welch** exist for efficiently decoding corrupted Reed-Solomon codewords, and understanding that recovery is *possible* (up to the distance bound) is part of why "closeness to a valid codeword" is a mathematically meaningful, well-behaved notion rather than a vague heuristic.

## Local Testing — the exact mechanism your FRI verifier uses
Testing whether a given string is a valid codeword (or close to one) by querying only a **small number of random positions** — not reading the whole string. **This is precisely what your STARK prover's verifier does**: rather than checking every evaluation in a trace/composition polynomial's full domain, it queries a small random sample and trusts the result with high probability. Local testing is the general theoretical category; FRI's query phase is a specific, concrete instance of it.

## Low-Degree Testing
The specific instance of local testing most relevant to you: testing whether a function's evaluations are **close to some low-degree polynomial's evaluations**, using only a few queries. This literally *is* what FRI does — "is this committed function actually (close to) a low-degree polynomial, or is it something else entirely" is the exact question FRI's folding-and-querying protocol answers.

## Proximity Testing — the crucial reframing that makes the whole system work
The key conceptual shift: FRI doesn't ask **"is this exactly a valid low-degree polynomial's evaluations?"** — it asks the much more tractable question **"is this close to (within some bounded distance of) some low-degree polynomial's evaluations?"** This reframing matters enormously for soundness analysis: a cheating prover's function is either genuinely low-degree (honest), or it's **far** from every low-degree polynomial (in which case random sampling catches it with high probability, connecting to your Concentration Bounds notes) — there's no need to handle a messy, hard-to-analyze "almost but not quite" middle ground precisely, because that middle ground barely exists for a well-chosen distance threshold. This proximity-based framing, rather than exact-membership testing, is *the* conceptual move that makes efficient, sound low-degree testing possible at all.

---

## Tying it back to your own STARK prover
Every evaluation-form polynomial in your STARK prover — trace polynomials, the composition polynomial — is, in coding-theory terms, **a Reed-Solomon codeword**. Your **blow-up factor** is literally the code's **1/rate**. Your **FRI queries** are a concrete instance of **local proximity testing** for the **low-degree testing** problem. And the reason a small number of random queries can catch a cheating prover with overwhelming probability isn't a lucky heuristic — it's a direct consequence of **Reed-Solomon's MDS distance property** combined with the **proximity (not exact-membership) framing**. This is the "why" underneath machinery you've already built and used.

---

## Quick self-check before moving to FRI (revisited)
You're ready to move on once you can, without notes:
1. Explain why Reed-Solomon codes achieving the Singleton bound (being MDS) is exactly why they were chosen as FRI's foundation
2. Explain the rate/blow-up-factor connection precisely, and the tradeoff it represents in real STARK implementation
3. Explain why proximity testing (rather than exact codeword-membership testing) is the framing that makes efficient low-degree testing possible
4. Explain, using coding theory language, exactly what your FRI verifier's query phase is doing
