# ZK Notes

**A structured curriculum map for learning Zero-Knowledge Proofs, from the underlying math to production zkVMs.**

ZK Notes is not a textbook. It's a set of 44 topic briefs that guide you through learning ZK from discrete math and linear algebra all the way to STARKs, PLONK, folding schemes, and real-world zkVMs like Cairo and Plonky3. Each brief tells you *where to study a topic from*, *how deep to go*, and *what you should be able to answer once you're done*. Nothing here assumes you already know cryptography: each phase builds on the last.

> 44 topics · 8 phases · zero to research-level ZK

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

### Phase 1: Mathematical Foundations
The language everything else is written in.

| Topic | Covers |
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

### Phase 2: Cryptography Foundations
The primitives ZK protocols are built out of.

| Topic | Covers |
|---|---|
| [`CryptographyFoundations.md`](./CryptographyFoundations.md) | Core cryptographic assumptions |
| [`Hashes.md`](./Hashes.md) | Hash functions, collision resistance |
| [`CryptographicCommitments.md`](./CryptographicCommitments.md) | Commitment schemes |
| [`EllipticCurveCryptography.md`](./EllipticCurveCryptography.md) | ECC fundamentals |
| [`Pairings.md`](./Pairings.md) | Bilinear pairings |

### Phase 3: Zero-Knowledge Fundamentals
Where "ZK" actually starts.

| Topic | Covers |
|---|---|
| [`Zero-KnowledgeFundamentals.md`](./Zero-KnowledgeFundamentals.md) | Completeness, soundness, zero-knowledge |
| [`InteractiveProofs.md`](./InteractiveProofs.md) | IP, interactive proof systems |
| [`ArithmeticCircuits.md`](./ArithmeticCircuits.md) | Representing computation as circuits |
| [`R1CS.md`](./R1CS.md) | Rank-1 Constraint Systems |
| [`QAP.md`](./QAP.md) | Quadratic Arithmetic Programs |
| [`AIR.md`](./AIR.md) | Algebraic Intermediate Representation |

### Phase 4: Core Proof Systems
The classic constructions everything else builds on.

| Topic | Covers |
|---|---|
| [`Groth16.md`](./Groth16.md) | Groth16 SNARK |
| [`Sumcheck.md`](./Sumcheck.md) | The Sumcheck protocol |
| [`FRI.md`](./FRI.md) | Fast Reed-Solomon IOP of Proximity |
| [`STARKs.md`](./STARKs.md) | Scalable Transparent ARguments of Knowledge |
| [`PLONK.md`](./PLONK.md) | PLONK proving system |
| [`Halo.md`](./Halo.md) | Halo / accumulation without trusted setup |
| [`UniversalSNARKs.md`](./UniversalSNARKs.md) | Universal & updatable SNARKs |
| [`LookupArguments.md`](./LookupArguments.md) | Lookup arguments (Plookup, logUp, etc.) |

### Phase 5: Advanced & Modern Constructions
Where current research lives.

| Topic | Covers |
|---|---|
| [`FoldingSchemes.md`](./FoldingSchemes.md) | Nova and folding-based recursion |
| [`RecursiveProofs.md`](./RecursiveProofs.md) | Proof composition & recursion |
| [`Plonky3.md`](./Plonky3.md) | Plonky3 framework |
| [`ModernProofSystems.md`](./ModernProofSystems.md) | Survey of state-of-the-art systems |
| [`AdvancedZKTheory.md`](./AdvancedZKTheory.md) | Deeper theoretical results |
| [`AdvancedCryptography.md`](./AdvancedCryptography.md) | Advanced primitives underpinning modern ZK |

### Phase 6: Engineering & Implementation
Building this stuff for real.

| Topic | Covers |
|---|---|
| [`ProverEngineering.md`](./ProverEngineering.md) | Practical prover design & performance |
| [`ZKOptimization.md`](./ZKOptimization.md) | Optimizing circuits and provers |
| [`ZKProtocolSecurity.md`](./ZKProtocolSecurity.md) | Protocol-level security pitfalls |
| [`SecurityEngineering.md`](./SecurityEngineering.md) | Secure implementation practices |
| [`ZKProgrammingLanguages.md`](./ZKProgrammingLanguages.md) | DSLs for writing circuits |
| [`Cairo.md`](./Cairo.md) | The Cairo language & VM |
| [`RustforZK.md`](./RustforZK.md) | Rust as the dominant ZK implementation language |

### Phase 7: Applications
Where ZK meets the real world.

| Topic | Covers |
|---|---|
| [`ZKApplicationLayer.md`](./ZKApplicationLayer.md) | Application design patterns |
| [`ZKRollups.md`](./ZKRollups.md) | ZK-rollups & scaling blockchains |
| [`zkVMs.md`](./zkVMs.md) | Zero-knowledge virtual machines |

### Phase 8: Research Frontier
For when you've cleared everything above.

| Topic | Covers |
|---|---|
| [`AdvancedResearch.md`](./AdvancedResearch.md) | Open problems & recent papers |

---

## Contributing

Better source links, sharper scope/depth guidance, and improved self-check questions are all welcome. Open an issue or PR. Please keep the sources, depth, questions structure and the phase/file-naming convention intact so the learning path stays navigable.

## License

Add a license (MIT or CC-BY-4.0 are common for educational content) so people know how they're allowed to reuse and share these topic briefs.

## Star / Follow

If this helped you learn ZK, a ⭐ helps other people find it.
