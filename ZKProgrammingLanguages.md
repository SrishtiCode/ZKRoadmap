# ZK Programming Languages — ZK Context Notes

**Sources:**

1. [Circom documentation](https://docs.circom.io/) (free). Terminology formalization for what you already know.

2. [Noir documentation](https://noir-lang.org/docs) (free). Official docs, the right primary source for a language.

3. [Leo documentation](https://docs.leo-lang.org/) (free) and the [ZoKrates GitHub repository and docs](https://github.com/Zokrates/ZoKrates) (free). For the "other ecosystems" SKIM-level entries.

4. Arun, Setty & Thaler, [*Jolt: SNARKs for Virtual Machines via Lookups*](https://eprint.iacr.org/2023/1217) (free, IACR ePrint). Worth reading directly given how different its architecture is from what you've studied so far.

---

## Circom 

**Signals**: the basic variable type — represents a wire/value in the circuit (input, output, or intermediate signals).

**Templates**: reusable, parameterized circuit definitions — analogous to a function or class in ordinary programming.

**Components**: instances of templates — instantiating a template creates a component, the circuit-building equivalent of calling a function or instantiating a class.

**Constraints — a critical distinction worth being precise about**: Circom has **two different operators** that look similar but behave very differently: `<==` and `===` actually **add R1CS constraints**; `<--` only performs **witness computation without adding any constraint**. This distinction is a genuine, real-world **source of under-constrained circuit bugs** — connecting directly to your security bug-bounty notes: a developer using `<--` when they meant `<==` gets a circuit that computes the right value in the honest case but **doesn't actually constrain it**, leaving room for a malicious witness. This is worth having completely automatic — it's one of the most common real Circom vulnerability patterns.

**Witness generation**: Circom compiles to a separate witness calculator (WASM or C++) that computes all signal values given inputs — distinct from the R1CS constraint file itself, which only defines what's valid, not how to compute it.

**R1CS**: Circom's actual compilation target — directly the R1CS you've studied in full depth.

**circomlib**: the standard library of common circuit templates (hash functions, comparators, bit operations). Reusing well-audited circomlib components instead of reimplementing common gadgets from scratch is standard, security-conscious practice.

**Groth16**: Circom circuits are most commonly paired with Groth16 (via the snarkjs toolchain) for actual proof generation, though newer tooling supports PLONK backends too.

---

## Noir 

**Noir syntax**: Rust-like, designed explicitly for **developer ergonomics** — a higher-level, more approachable language than Circom's more circuit-literal style.

**Private/public inputs**: explicit syntax marking which inputs are private witness data versus public — more ergonomic than manually tracking this distinction the way lower-level circuit languages require.

**Constraints & Assertions**: Noir compiles high-level code (arithmetic, `assert()` statements) down to constraints **automatically** — `assert()` in Noir generates actual circuit constraints, directly analogous to Circom's `===`, but Noir abstracts away much more of the manual constraint-writing burden than Circom does.

**ACIR (Abstract Circuit Intermediate Representation) — Noir's key architectural differentiator**: Noir compiles to its **own intermediate representation**, which is **backend-agnostic** — not tied directly to R1CS or any single specific proving system. This is a genuinely important design difference from Circom: **the same Noir circuit code can target different underlying proving systems** depending on which backend gets plugged in, rather than being tightly coupled to one specific workflow (as Circom effectively is to R1CS/Groth16-style tooling).

**Backend Architecture**: ACIR gets compiled further by a **chosen backend** — most commonly Aztec's **Barretenberg** (an UltraPlonk-based system), but the architecture is designed to support other backends too. This backend-swappable design is precisely what "backend-agnostic" means in practice — Noir the language is decoupled from Noir the proving system.

**Witness Generation, Proof Generation, Verification**: handled by whichever backend is plugged in, following the same general witness→proof→verify pipeline you already understand deeply, just with Noir/ACIR sitting as a portable front-end on top.

---

## Cairo (recap)
Fully covered in your dedicated Cairo notes — STARK-friendly language and VM, non-deterministic memory via permutation arguments, builtins as custom-gate equivalents.

---

## Other Ecosystems

**Leo**: Aleo's high-level language, sharing similar developer-ergonomics goals with Noir, compiling down to Aleo's snarkVM proving system (Varuna/Marlin-based, per your earlier company research). SKIM-level for now — recognize the name and rough positioning rather than deep fluency.

**ZoKrates**: One of the earliest high-level ZK DSLs, historically important (predates Circom and Noir in the space), Python-like syntax. Less actively dominant in current production use, but worth recognizing by name given its historical role in popularizing accessible ZK circuit-writing.

**RISC Zero**: Recap pointer — proves arbitrary Rust programs compiled to RISC-V, STARK-based. **Full depth deep-dive scheduled for Week 3** alongside your zkVM company study.

**SP1**: Recap pointer — Succinct's zkVM, Plonky3-based. **Full depth deep-dive scheduled for Week 3.**

**Jolt — genuinely different architecture, worth real explanation now**
Jolt takes a fundamentally different approach from RISC Zero and SP1's more traditional constraint-based zkVM design. Instead of building custom AIR/R1CS-style constraints encoding each RISC-V instruction's semantics, **Jolt proves CPU execution almost entirely via massive lookup arguments** — checking each instruction's behavior against enormous precomputed lookup tables, using efficient lookup-argument and **sumcheck-based** techniques (directly connecting forward to your upcoming §31 Sumcheck deep-dive). This "lookup singularity" philosophy — reduce nearly everything to lookups rather than hand-crafted algebraic constraints — is a genuinely different design philosophy from the AIR-constraint-heavy approach you've built deep intuition for through Cairo, RISC Zero, and SP1. Worth holding Jolt as a concrete example of "there's more than one way to build a zkVM," and worth revisiting once your Sumcheck deep-dive is done, since Jolt will make significantly more sense with that foundation in place.

---

## Quick self-check before moving to zkVMs (full depth)
You're ready to move on once you can, without notes:
1. Explain the Circom `<==`/`===` vs `<--` distinction precisely, and why confusing them is a real security bug pattern
2. Explain what ACIR is and why Noir's backend-agnostic design is architecturally different from Circom's more direct R1CS coupling
3. Explain, at a high level, how Jolt's "lookup singularity" approach differs philosophically from RISC Zero/SP1's constraint-based zkVM design
4. Name which upcoming topic you'll want to revisit Jolt alongside, and why
