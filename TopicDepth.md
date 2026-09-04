# ZK Cryptography Topic Depth Guide
**How deep to study each of the 44 topic areas, assuming zero starting knowledge.**

## Depth Levels (used throughout)

- **SKIM** — Know it exists, know the one-sentence definition, know when to look it up. You should be able to recognize the term in a paper and not be lost, but you won't use it hands-on.
- **SOLID** — Can explain it correctly to someone else, can use it in code/practice, can reason about it without a reference open. This is "competent working developer" level.
- **DEEP** — Can derive it, can explain *why* it's sound (not just that it is), can compare it against alternatives with real tradeoffs, could teach it, could spot a subtle bug in someone else's implementation of it. This is "the people hiring you would trust your judgment on this" level.

A rough calibration: most working ZK engineers stop at SOLID for ~80% of this list and go DEEP on maybe 8–10 topics that form their specialty. Going DEEP on everything isn't the goal — going DEEP on the *right* things while staying SOLID everywhere else is.

---

## 1. Math Foundations

| Topic | Depth | Why / What "enough" looks like |
|---|---|---|
| Discrete Math (sets, logic, proof techniques, combinatorics) | **SOLID** | You need fluent proof-reading (direct/contradiction/induction) to follow any crypto paper. You don't need to be a combinatorics researcher. |
| Linear Algebra | **SOLID** | Needed constantly (vector spaces underpin multilinear polynomials, MSM). Stop before you're doing pure-math linear algebra proofs — application fluency is enough. |
| Probability | **SOLID** | You need this specifically for soundness-error reasoning ("probability a cheating prover succeeds is negligible"). Concentration bounds and negligible-probability reasoning matter more than general probability theory. |

## 2. Number Theory
**DEEP.** This is genuinely foundational — modular arithmetic, Fermat/Euler theorems, and the discrete log problem are load-bearing for every construction downstream (finite fields, elliptic curves, RSA-adjacent assumptions). Spend real time until modular inverse computation and CRT feel automatic, not looked-up.

## 3. Abstract Algebra
**DEEP.** Groups, rings, fields — especially finite fields and field extensions — are the literal language every ZK paper is written in. You cannot skim this and expect Groth16, STARKs, or pairings to make sense later. If you only go deep on one "pure math" section this month, make it this one and Polynomial Math (§4).

## 4. Polynomial Mathematics ⭐
**DEEP.** You've already used this (KZG, your STARK prover), so this is "deepen," not "learn from zero." Push until vanishing polynomials, interpolation, and coefficient↔evaluation-form conversion are second nature — this is the substrate of every proof system you'll study this month.

## 5. FFT / NTT
**DEEP.** This is a Tier-1 prover-engineering skill, not just math. You should be able to implement radix-2 FFT over a finite field from memory, understand why NTT needs roots of unity of the right order, and know why this is the bottleneck operation in most provers.

## 6. Computational Complexity
**SOLID.** You need P/NP/NP-complete/reductions fluently enough to understand why "circuit satisfiability" is the right target language for SNARKs. You do **not** need to go deep into complexity theory research (that's Tier 4 territory, only relevant if you're aiming at PCP-theorem-level theory later).

## 7. Cryptography Foundations
**DEEP.** Security games, computational vs. statistical security, the random oracle model, and Fiat-Shamir are the vocabulary every soundness argument is written in. If you can't state what "computationally indistinguishable" means precisely, you can't actually evaluate whether a construction is secure — you're just trusting the author.

## 8. Cryptographic Commitments
**DEEP.** Directly maps to what you've built (KZG) and what you're studying (IPA in Halo2, FRI-based commitments in STARKs). Know hiding vs. binding, why each property matters, and be able to explain what breaks if a commitment scheme is only computationally (not statistically) binding.

## 9. Elliptic Curve Cryptography ⭐⭐⭐⭐⭐
**DEEP.** This was flagged as your real current gap. You've used BLS12-381 but likely haven't derived point addition/doubling formulas, understood cofactor clearing, or reasoned about why specific curves (BN254 vs. BLS12-381 vs. Pasta) get chosen for specific use cases. This is a "sit down with Costello's pairings paper and actually do the algebra" topic, not a skim.

## 10. Pairings ⭐⭐⭐⭐⭐
**DEEP.** Same tier as above, and connected — Miller loop, final exponentiation, embedding degree. This is the single most technically demanding topic on the whole list relative to how little most ZK developers actually understand it (most just call a library function). Going deep here is a genuine differentiator.

## 11. Zero-Knowledge Fundamentals
**DEEP.** Completeness, soundness, zero-knowledge, knowledge soundness, extractors, simulation — these five properties are what you'll be asked to reason about for *every single protocol* for the rest of your career in this field. Get these definitions exactly right, not approximately right.

## 12. Interactive Proofs
**DEEP.** Sigma protocols, Fiat-Shamir transformation, IOPs (Interactive Oracle Proofs) — this is the conceptual bridge between "classical ZK proofs" and "modern SNARK/STARK constructions." Both Groth16's lineage and STARKs route through this framework.

## 13. Arithmetic Circuits
**SOLID → DEEP.** You already work with these daily (your STARK prover, R1CS work). Push toward DEEP specifically on constraint systems and circuit optimization — this is close to your practical strength already.

## 14. R1CS ⭐⭐⭐⭐⭐
**DEEP.** You've implemented this. Make sure you can explain R1CS→QAP reduction to someone else without notes — that's your actual bar here, not new learning.

## 15. QAP ⭐⭐⭐⭐⭐
**DEEP.** Same — you're mid-way through this via the GGPR13 paper. Push until the equation A(X)B(X)−C(X)=h(X)t(X) is something you could derive, not just recognize.

## 16. Groth16 ⭐⭐⭐⭐⭐
**DEEP.** Your current active study. Full depth target: could explain toxic waste / trusted setup risk, walk through proof generation and pairing verification, and explain why it achieves knowledge soundness — to someone else, from memory.

## 17. Universal SNARKs
**SOLID.** Understand what "universal/updatable setup" means and why Powers of Tau matters (it lets many circuits share one ceremony). You don't need to derive the SRS construction from scratch — understanding the *tradeoff* it represents (vs. circuit-specific setup) is enough.

## 18. PLONK
**DEEP.** Core to your Week 2 plan and to half your target companies (anything Halo2-adjacent is PLONK-family). Permutation arguments and custom gates should be DEEP; the full protocol should be something you could explain end-to-end.

## 19. Lookup Arguments
**DEEP.** Increasingly central to production circuit design (range checks, table lookups) — Halo2, Plonky3, and most modern systems lean heavily on these. Plookup and the multiset-equality trick specifically deserve real derivation time.

## 20. Halo / Halo2
**DEEP.** Your Week 2 target. PLONKish arithmetization, advice/fixed/instance columns, and how Halo2 achieves recursion without trusted setup (accumulation) should all be DEEP by end of that week.

## 21. STARKs ⭐⭐⭐⭐⭐
**DEEP.** Already built. Same note as R1CS/QAP — this is "deepen and be able to teach it," not new learning.

## 22. AIR ⭐⭐⭐⭐⭐
**DEEP.** Same as STARKs — you've implemented this. Make sure transition constraints vs. boundary constraints vs. composition polynomial are crisp, distinct concepts in your head, not blurred together.

## 23. Coding Theory ⭐⭐⭐⭐⭐
**DEEP — and this is a real gap for you specifically.** I flagged this before: most people implement FRI without understanding Reed-Solomon codes deeply enough to explain *why* proximity testing works. This is worth dedicated study time, not folding into your FRI review.

## 24. FRI ⭐⭐⭐⭐⭐
**DEEP.** Already built. Push toward understanding the soundness analysis (not just the folding mechanics) — this is where Coding Theory (§23) and FRI meet, and it's a common gap even among people who've implemented FRI successfully.

## 25. Cairo
**SOLID.** This is developer fluency, not cryptographic depth — you need to write Cairo programs and understand the VM's execution/memory model well, but you don't need to derive StarkNet's specific AIR from scratch (that's covered by your STARK/AIR depth already).

## 26. Plonky3
**SOLID → DEEP on the parts that overlap STARKs/FRI (which you already have).** The genuinely new parts (custom fields, SIMD/parallel proving specifics) can stay SOLID — this is architecture-reading depth, not reimplementation depth, in September.

## 27. ZK Programming Languages (Circom, Noir, Leo, ZoKrates)
**SOLID.** You already know Circom. Get Noir to the same level (write real circuits fluently). Leo/ZoKrates: SKIM is fine — know they exist and roughly what niche they fill, don't invest real time unless a target company specifically uses them.

## 28. zkVMs ⭐⭐⭐⭐⭐
**DEEP.** This is a core target area (Week 3, half your top-10 list). Instruction sets, memory checking, continuations — go deep here specifically because it's where your existing EVM-opcode-testing background gives you a real head start.

## 29. Recursive Proofs
**SOLID → DEEP on concepts, SOLID on implementation.** Understand proof composition and accumulation schemes well enough to explain why recursion is hard (differing curves/fields between layers, the curve-cycle trick). Full implementation depth can wait.

## 30. Folding Schemes (Nova, SuperNova, HyperNova)
**SOLID.** Understand the core idea (folding two instances into one instead of full recursion) and why it's cheaper than naive recursive SNARKs. This connects to Nexus's earlier architecture. Don't implement a folding scheme this month — conceptual fluency is the right bar.

## 31. Sumcheck ⭐⭐⭐⭐⭐
**DEEP — flagged gap, add to your roadmap explicitly.** This underlies GKR, Spartan, HyperPlonk and is increasingly central to newer systems. Worth a dedicated few days even though it's not in your original week-by-week plan.

## 32. Multilinear Algebra
**DEEP** (paired with Sumcheck — you can't really go deep on one without the other). Multilinear extensions and the Boolean hypercube are the natural language sumcheck is written in.

## 33. Modern Proof Systems (survey list: Marlin, Sonic, Spartan, Ligero, Bulletproofs, etc.)
**SKIM, deliberately.** The document says this explicitly: understand what problem each protocol solves differently, don't memorize each one's internals. Treat this section as a map you consult, not a syllabus you complete. Exception: if a specific target company uses one (e.g., Bulletproofs shows up in some privacy-focused systems), upgrade that one protocol to SOLID.

## 34. Hashes for ZK (Poseidon, Rescue, MiMC)
**SOLID.** You need to understand *why* SHA-256 is expensive in-circuit and why algebraic hashes like Poseidon are designed differently (fewer, algebraically-simple constraints). You don't need to design a new ZK-friendly hash — using and reasoning about existing ones is enough.

## 35. ZK Optimization
**SOLID**, learned contextually rather than standalone. You'll pick up constraint reduction, custom gates, and lookup optimization naturally while doing Halo2/Plonky3 work — don't front-load this as separate study.

## 36. Prover Engineering
**DEEP.** This is a core practical skill for the actual job you want. FFT/MSM implementation, field arithmetic, parallelization, benchmarking/profiling — this is where "understands the theory" turns into "can ship a fast prover," which is what companies are actually paying for.

## 37. Rust for ZK
**DEEP.** You're at medium already; push to genuinely advanced — trait design for field/group abstractions (study arkworks' traits as a model), lifetimes in performance-critical code, and comfort with `unsafe` where provers need it for speed.

## 38. Security Engineering (trusted setup risk, subgroup attacks, timing attacks)
**SOLID.** Given you've deliberately chosen dev-track over security-track, this is your ceiling here — know the attack classes exist, know how to avoid the common ones (e.g., always validate curve points are in the correct subgroup), but don't go deep into offensive security research.

## 39. ZK Protocol Security (per-system security properties, reduction proofs)
**SOLID → DEEP on the definitions (covered already in §11), SOLID on applying them.** You should be able to look at a new protocol and ask "what's the soundness error, what's the trusted setup model" — but constructing novel security reductions yourself is genuinely research-level and can wait.

## 40. ZK Application Layer (identity, voting, zkTLS, zkEmail, etc.)
**SKIM.** Context only — useful for understanding *why* the infrastructure you're building matters, not something to study deeply unless you're targeting an applications-layer company specifically.

## 41. ZK Rollups
**SOLID.** Several of your top-10 companies are rollup teams — understand state transition proofs, sequencers vs. provers vs. verifiers, and data availability at a real working level, since this is the product context your prover work sits inside.

## 42. Advanced Cryptography (AGM, GGM, forking lemma, rewinding, knowledge assumptions)
**DEFER — target DEEP eventually, but not in September.** This is genuinely PhD-adjacent material and is what separates "strong engineer" from "can publish/review novel cryptographic constructions." Real target for your stated "compare to industry cryptographers" goal, but attempting it now, underneath everything else, would spread you too thin. Revisit in Q4/2027 once Tier-1 foundations are airtight.

## 43. Advanced ZK Theory (PCP theorem, IOP formalism, GKR)
**DEFER**, same reasoning as §42. SKIM level is fine for now (know PCP theorem exists and roughly what it says) — full derivation depth is a later-stage goal.

## 44. Advanced Research Areas (PQ-ZK, lattice-based ZK, ZK hardware)
**SKIM, ongoing awareness only.** This is "read a survey paper every few months to stay current," not active study. Genuinely research-frontier material — useful to know where the field is heading, not necessary for the job you're pursuing right now.

---

## Summary: your DEEP list (the ~19 topics worth real mastery this cycle)

Number Theory · Abstract Algebra · Polynomial Math · FFT/NTT · Crypto Foundations · Cryptographic Commitments · Elliptic Curves · Pairings · ZK Fundamentals · Interactive Proofs · R1CS · QAP · Groth16 · PLONK · Lookup Arguments · Halo2 · STARKs · AIR · Coding Theory · FRI · zkVMs · Sumcheck · Multilinear Algebra · Prover Engineering · Rust for ZK

That's already a lot — 25 items, not 10. Notice most of them are things you're already actively working through (Weeks 1–4 of your roadmap cover the large majority). The genuinely *new* additions this document surfaces that weren't explicitly in your roadmap: **Coding Theory** and **Sumcheck/Multilinear Algebra** — both worth deliberately scheduling in, likely by trimming a day from Week 4's lighter-touch sections (Aleo/Mina) to make room.

## Everything else
SOLID is a real, respectable, sufficient level for a working ZK developer — it is not "the lesser option." The topics marked DEFER (§42–44) are there so you don't feel behind for not having studied them; they're legitimately next-cycle material, not gaps in your current competence.
