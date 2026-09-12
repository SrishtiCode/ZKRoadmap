# ZK Notes

**A structured curriculum map for learning Zero-Knowledge Proofs, from the underlying math to production zkVMs.**

ZK Notes is not a textbook. It's a set of topics that guide you through learning ZK from discrete math and linear algebra all the way to STARKs, PLONK, folding schemes, and real-world zkVMs like Cairo and Plonky3. Each brief tells you *where to study a topic from*, *how deep to go*, and *what you should be able to answer once you're done*. Nothing here assumes you already know cryptography: each phase builds on the last.

---

## What each file actually is

This repo doesn't try to re-teach material that's already taught well elsewhere. Every file follows the same shape:

1. **Study sources** (top of the file): the best free resource(s) to actually learn the topic from, e.g. an MIT OCW course, a textbook, or a foundational paper.
2. **Depth level**: one of SKIM / SOLID / DEEP (see below), telling you what level of understanding is expected for this topic before moving on.
3. **Self-check questions** (end of the file): the questions you should be able to answer once you've studied the topic, so you can confirm you actually learned it rather than just skimmed it.

Think of each file as a syllabus entry plus a comprehension check, not a substitute for the source material it points to.

---

## Depth Level (used throughout)

Every topic is tagged with one of three target depths. This tells you how far to take the source material before moving on. Going deeper than the tag is never wrong, but going shallower means you'll likely hit gaps later.

- **SKIM**: Know it exists, know the one-sentence definition, know when to look it up. You should be able to recognize the term in a paper and not be lost, but you won't use it hands-on.
- **SOLID**: Can explain it correctly to someone else, can use it in code/practice, can reason about it without a reference open. This is "competent working developer" level.
- **DEEP**: Can derive it, can explain *why* it's sound (not just that it is), can compare it against alternatives with real tradeoffs, could teach it, could spot a subtle bug in someone else's implementation of it. This is "the people hiring you would trust your judgment on this" level.

---

## Why this exists

Most ZK learning material assumes you already have a strong crypto or math background, or it jumps straight into "here's how Groth16 works" without ever explaining *why* a QAP exists or what a commitment scheme actually commits to. ZK Notes is an attempt to fix that: a linear, dependency-aware path that tells you exactly where to learn each prerequisite and how to know when you've actually learned it.

---

## How to use this repo

1. Start at **Phase 1** even if you think you know the math. The later topics assume this vocabulary.
2. For each topic: read the linked source(s) first, study to the stated depth, then answer the self-check questions at the end before moving on.
3. Work through the phases roughly in order. Within a phase, topics are largely independent.
4. Once you hit Phase 6+, start reading real implementations (Halo2, Plonky3, Cairo) side by side with the topic briefs.

---

## Free companion resources

Every topic file opens with a link to the best free resource for that subject: MIT OpenCourseWare lecture series, Victor Shoup's *A Computational Introduction to Number Theory and Algebra*, foundational papers on arXiv/eprint.iacr.org, and similar. These are where you should actually study from; the file itself is the scope and the self-check, not the lesson.

---

## Learning Path

### Phase 1: Mathematical Foundations (1–6)
The language everything else is written in.

| # | Topic | File | Covers |
|---|---|---|---|
| 1.1 | Discrete Mathematics | [`DiscreteMathematics.md`](./DiscreteMathematics.md) | Sets, logic, proof techniques, combinatorics, graph theory |
| 1.2 | Linear Algebra | [`LinearAlgebra.md`](./LinearAlgebra.md) | Vector spaces, matrices, eigenvalues, linear transformations |
| 1.3 | Probability | [`Probability.md`](./Probability.md) | Distributions, birthday paradox, union bound, concentration bounds |
| 2 | Number Theory | [`NumberTheory.md`](./NumberTheory.md) | Modular arithmetic, Fermat/Euler, CRT, discrete log |
| 3 | Abstract Algebra | [`AbstractAlgebra.md`](./AbstractAlgebra.md) | Groups, rings, fields, finite fields |
| 4  | Polynomial Mathematics | [`PolynomialMathematics.md`](./PolynomialMathematics.md) | Representation, interpolation, vanishing polynomials |
| 5 | FFT / NTT | [`FFT.md`](./FFT.md) | Roots of unity, Cooley-Tukey, polynomial evaluation via FFT |
| 6 | Computational Complexity | [`ComputationalComplexity.md`](./ComputationalComplexity.md) | Asymptotic notation, P/NP, reductions |

### Phase 2: Cryptography Foundations (7–10)
The primitives ZK protocols are built out of.

| # | Topic | File | Covers |
|---|---|---|---|
| 7 | Cryptography Foundations | [`CryptographyFoundations.md`](./CryptographyFoundations.md) | Security definitions, adversaries, random oracle model |
| 8 | Cryptographic Commitments | [`CryptographicCommitments.md`](./CryptographicCommitments.md) | Hiding/binding, Pedersen, polynomial commitments |
| 9 | Elliptic Curve Cryptography | [`EllipticCurveCryptography.md`](./EllipticCurveCryptography.md) | Curve arithmetic, MSM, BN254, BLS12-381 |
| 10 | Pairings | [`Pairings.md`](./Pairings.md) | Bilinear maps, Miller loop, pairing-friendly curves |

### Phase 3: Zero-Knowledge Fundamentals (11–15)
Where "ZK" actually starts.

| # | Topic | File | Covers |
|---|---|---|---|
| 11 | Zero-Knowledge Fundamentals | [`Zero-KnowledgeFundamentals.md`](./Zero-KnowledgeFundamentals.md) | Completeness, soundness, zero-knowledge, extractors |
| 12 | Interactive Proofs | [`InteractiveProofs.md`](./InteractiveProofs.md) | Sigma protocols, Fiat-Shamir, IOPs, PCPs |
| 13 | Arithmetic Circuits | [`ArithmeticCircuits.md`](./ArithmeticCircuits.md) | Circuit representation, constraint systems |
| 14 | R1CS | [`R1CS.md`](./R1CS.md) | Constraint matrices, witness vectors, R1CS → QAP |
| 15 | QAP | [`QAP.md`](./QAP.md) | Quadratic Arithmetic Programs, vanishing/quotient polynomials |

### Phase 4: Core Proof Systems (16–24)
The constructions everything downstream builds on.

| # | Topic | File | Covers |
|---|---|---|---|
| 16 | Groth16 | [`Groth16.md`](./Groth16.md) | Trusted setup, proving/verification keys, pairing verification |
| 17 | Universal SNARKs | [`UniversalSNARKs.md`](./UniversalSNARKs.md) | Universal & updatable setups, Powers of Tau |
| 18 | PLONK | [`PLONK.md`](./PLONK.md) | PLONKish arithmetization, permutation arguments, custom gates |
| 19 | Lookup Arguments | [`LookupArguments.md`](./LookupArguments.md) | Plookup, LogUp, range checks |
| 20 | Halo / Halo2 | [`Halo.md`](./Halo.md) | PLONKish circuits, accumulation, recursion without trusted setup |
| 21 | STARKs | [`STARKs.md`](./STARKs.md) | Transparent proofs, execution traces, low-degree testing |
| 22 | AIR | [`AIR.md`](./AIR.md) | Algebraic Intermediate Representation, transition constraints, DEEP-ALI |
| 23 | Coding Theory | [`CodingTheory.md`](./CodingTheory.md) | Reed-Solomon codes, Hamming distance, proximity testing |
| 24 | FRI | [`FRI.md`](./FRI.md) | Polynomial folding, query phase, DEEP-FRI |

### Phase 5: Advanced & Modern Constructions (25–33)
Where current research and production zkVMs live.

| # | Topic | File | Covers |
|---|---|---|---|
| 25 | Cairo | [`Cairo.md`](./Cairo.md) | Cairo language & VM, execution/memory model |
| 26 | Plonky3 | [`Plonky3.md`](./Plonky3.md) | Plonky3 architecture, SIMD, parallel proving |
| 27 | ZK Programming Languages | [`ZKProgrammingLanguages.md`](./ZKProgrammingLanguages.md) | Circom, Noir, Cairo, Leo, ZoKrates |
| 28 | zkVMs | [`zkVMs.md`](./zkVMs.md) | zkVM architecture, RISC Zero, SP1, Jolt |
| 29 | Recursive Proofs | [`RecursiveProofs.md`](./RecursiveProofs.md) | Proof composition, aggregation, curve cycles |
| 30 | Folding Schemes | [`FoldingSchemes.md`](./FoldingSchemes.md) | Nova, SuperNova, HyperNova, IVC |
| 31 | Sumcheck | [`Sumcheck.md`](./Sumcheck.md) | Sumcheck protocol, GKR, Spartan, HyperPlonk |
| 32 | Multilinear Algebra | [`MultilinearAlgebra.md`](./MultilinearAlgebra.md) | Multilinear extensions, tensor products, multilinear commitments |
| 33 | Modern Proof Systems | [`ModernProofSystems.md`](./ModernProofSystems.md) | Survey: Marlin, Sonic, Spartan, Bulletproofs, Orion, and more |

### Phase 6: Engineering & Implementation (34–39)
Building this stuff for real.

| # | Topic | File | Covers |
|---|---|---|---|
| 34 | Hashes for ZK | [`Hashes.md`](./Hashes.md) | Poseidon, Rescue, Griffin, why SHA-256 is expensive in-circuit |
| 35 | ZK Optimization | [`ZKOptimization.md`](./ZKOptimization.md) | Constraint reduction, MSM/FFT optimization, batching |
| 36 | Prover Engineering | [`ProverEngineering.md`](./ProverEngineering.md) | FFT/NTT/MSM implementation, GPU proving, parallelization |
| 37 | Rust for ZK | [`RustforZK.md`](./RustforZK.md) | Field/group traits, curve implementations, ZK-specific Rust patterns |
| 38 | Security Engineering | [`SecurityEngineering.md`](./SecurityEngineering.md) | Trusted setup security, ceremony design, side-channel attacks |
| 39 | ZK Protocol Security | [`ZKProtocolSecurity.md`](./ZKProtocolSecurity.md) | Soundness error, AGM/ROM, reduction proofs |

### Phase 7: Applications (40–41)
Where ZK meets the real world.

| # | Topic | File | Covers |
|---|---|---|---|
| 40 | ZK Application Layer | [`ZKApplicationLayer.md`](./ZKApplicationLayer.md) | ZK identity, voting, ZKML, zkTLS, zkEmail |
| 41 | ZK Rollups | [`ZKRollups.md`](./ZKRollups.md) | State transition proofs, sequencers, data availability |

### Phase 8: Advanced & Research (42–44)
For when you've cleared everything above.

| # | Topic | File | Covers |
|---|---|---|---|
| 42 | Advanced Cryptography | [`AdvancedCryptography.md`](./AdvancedCryptography.md) | Knowledge assumptions, AGM, forking lemma |
| 43 | Advanced ZK Theory | [`AdvancedZKTheory.md`](./AdvancedZKTheory.md) | PCP theorem, PIOP, accumulation schemes, IVC |
| 44 | Advanced Research Areas | [`AdvancedResearch.md`](./AdvancedResearch.md) | Post-quantum ZK, lattice/hash-based ZK, proof-carrying data |

---

## Contributing

Better source links, sharper scope/depth guidance, and improved self-check questions are all welcome. Open an issue or PR. Please keep the sources, depth, questions structure and the phase/file-naming convention intact so the learning path stays navigable.

## License

Add a license (MIT or CC-BY-4.0 are common for educational content) so people know how they're allowed to reuse and share these topic briefs.

## Star / Follow

If this helped you learn ZK, a ⭐ helps other people find it.
