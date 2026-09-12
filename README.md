# ZK Notes

**A structured, from-scratch curriculum for learning Zero-Knowledge Proofs — from the underlying math to production zkVMs.**

ZK Notes is a collection of self-contained study notes that take you from discrete math and linear algebra all the way to STARKs, PLONK, folding schemes, and real-world zkVMs like Cairo and Plonky3. Nothing here assumes you already know cryptography — each phase builds on the last.

> 44 notes · 8 phases · zero → research-level ZK

---

## Why this exists

Most ZK learning material assumes you already have a strong crypto or math background, or it jumps straight into "here's how Groth16 works" without ever explaining *why* a QAP exists or what a commitment scheme actually commits to. ZK Notes is an attempt to fix that: a linear, dependency-aware path where every note tells you what you need to already know, and what it unlocks next.

---

## How to use this repo

1. Start at **Phase 1** even if you think you know the math — the later notes assume this vocabulary.
2. Work through the phases roughly in order. Within a phase, files are largely independent.
3. Use `TopicDepth.md` as a reference for how deep each topic goes and where to stop if you're short on time.
4. Once you hit Phase 6+, start reading real implementations (Halo2, Plonky3, Cairo) side by side with the notes.

---

## 🗺️ Learning Path

### Phase 1 — Mathematical Foundations
The language everything else is written in.

| Note | Covers |
|---|---|
| [`DiscreteMathematics.md`](./DiscreteMathematics.md) | Sets, logic, modular arithmetic |
| [`LinearAlgebra.md`](./LinearAlgebra.md) | Vector spaces, matrices, transformations |
| [`AbstractAlgebra.md`](./AbstractAlgebra.md) | Groups, rings, fields |
| [`NumberTheory.md`](./NumberTheory.md) | Primes, modular inverses, order |
| [`Probability.md`](./Probability.md) | Distributions, soundness/randomness intuition |
| [`PolynomialMathematics.md`](./PolynomialMathematics.md) | Polynomial rings, interpolation, evaluation |
| [`MultilinearAlgebra.md`](./MultilinearAlgebra.md) | Multilinear extensions, tensors |
| [`FFT.md`](./FFT.md) | Fast Fourier Transform over finite fields |
| [`ComputationalComplexity.md`](./ComputationalComplexity.md) | P/NP, complexity classes relevant to proofs |
| [`CodingTheory.md`](./CodingTheory.md) | Error-correcting codes, Reed-Solomon |

### Phase 2 — Cryptography Foundations
The primitives ZK protocols are built out of.

| Note | Covers |
|---|---|
| [`CryptographyFoundations.md`](./CryptographyFoundations.md) | Core cryptographic assumptions |
| [`Hashes.md`](./Hashes.md) | Hash functions, collision resistance |
| [`CryptographicCommitments.md`](./CryptographicCommitments.md) | Commitment schemes |
| [`EllipticCurveCryptography.md`](./EllipticCurveCryptography.md) | ECC fundamentals |
| [`Pairings.md`](./Pairings.md) | Bilinear pairings |

### Phase 3 — Zero-Knowledge Fundamentals
Where "ZK" actually starts.

| Note | Covers |
|---|---|
| [`Zero-KnowledgeFundamentals.md`](./Zero-KnowledgeFundamentals.md) | Completeness, soundness, zero-knowledge |
| [`InteractiveProofs.md`](./InteractiveProofs.md) | IP, interactive proof systems |
| [`ArithmeticCircuits.md`](./ArithmeticCircuits.md) | Representing computation as circuits |
| [`R1CS.md`](./R1CS.md) | Rank-1 Constraint Systems |
| [`QAP.md`](./QAP.md) | Quadratic Arithmetic Programs |
| [`AIR.md`](./AIR.md) | Algebraic Intermediate Representation |

### Phase 4 — Core Proof Systems
The classic constructions everything else builds on.

| Note | Covers |
|---|---|
| [`Groth16.md`](./Groth16.md) | Groth16 SNARK |
| [`Sumcheck.md`](./Sumcheck.md) | The Sumcheck protocol |
| [`FRI.md`](./FRI.md) | Fast Reed-Solomon IOP of Proximity |
| [`STARKs.md`](./STARKs.md) | Scalable Transparent ARguments of Knowledge |
| [`PLONK.md`](./PLONK.md) | PLONK proving system |
| [`Halo.md`](./Halo.md) | Halo / accumulation without trusted setup |
| [`UniversalSNARKs.md`](./UniversalSNARKs.md) | Universal & updatable SNARKs |
| [`LookupArguments.md`](./LookupArguments.md) | Lookup arguments (Plookup, logUp, etc.) |

### Phase 5 — Advanced & Modern Constructions
Where current research lives.

| Note | Covers |
|---|---|
| [`FoldingSchemes.md`](./FoldingSchemes.md) | Nova and folding-based recursion |
| [`RecursiveProofs.md`](./RecursiveProofs.md) | Proof composition & recursion |
| [`Plonky3.md`](./Plonky3.md) | Plonky3 framework |
| [`ModernProofSystems.md`](./ModernProofSystems.md) | Survey of state-of-the-art systems |
| [`AdvancedZKTheory.md`](./AdvancedZKTheory.md) | Deeper theoretical results |
| [`AdvancedCryptography.md`](./AdvancedCryptography.md) | Advanced primitives underpinning modern ZK |

### Phase 6 — Engineering & Implementation
Building this stuff for real.

| Note | Covers |
|---|---|
| [`ProverEngineering.md`](./ProverEngineering.md) | Practical prover design & performance |
| [`ZKOptimization.md`](./ZKOptimization.md) | Optimizing circuits and provers |
| [`ZKProtocolSecurity.md`](./ZKProtocolSecurity.md) | Protocol-level security pitfalls |
| [`SecurityEngineering.md`](./SecurityEngineering.md) | Secure implementation practices |
| [`ZKProgrammingLanguages.md`](./ZKProgrammingLanguages.md) | DSLs for writing circuits |
| [`Cairo.md`](./Cairo.md) | The Cairo language & VM |
| [`RustforZK.md`](./RustforZK.md) | Rust as the dominant ZK implementation language |

### Phase 7 — Applications
Where ZK meets the real world.

| Note | Covers |
|---|---|
| [`ZKApplicationLayer.md`](./ZKApplicationLayer.md) | Application design patterns |
| [`ZKRollups.md`](./ZKRollups.md) | ZK-rollups & scaling blockchains |
| [`zkVMs.md`](./zkVMs.md) | Zero-knowledge virtual machines |

### Phase 8 — Research Frontier
For when you've cleared everything above.

| Note | Covers |
|---|---|
| [`AdvancedResearch.md`](./AdvancedResearch.md) | Open problems & recent papers |
| [`TopicDepth.md`](./TopicDepth.md) | Guide to how deep to go per topic |

---

## Contributing

Corrections, clearer explanations, diagrams, and additional worked examples are welcome. Open an issue or PR — please keep the phase structure and file-naming convention intact so the learning path stays navigable.

## License

Add a license (MIT or CC-BY-4.0 are common for educational content) so people know how they're allowed to reuse and share these notes.

## Star / Follow

If this helped you learn ZK, a ⭐ helps other people find it.
