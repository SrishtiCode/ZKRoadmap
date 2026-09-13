# zkVMs (DEEP)

**Sources:**

1. [RISC Zero documentation](https://dev.risczero.com/) and [SP1 documentation](https://docs.succinct.xyz/) (both free). The two primary systems on your original company-study list, both practical, actively maintained sources.

2. Arun, Setty & Thaler, [*Jolt: SNARKs for Virtual Machines via Lookups*](https://eprint.iacr.org/2023/1217) (already covered) for the lookup-singularity architecture.

3. [Valida documentation](https://lita.gitbook.io/lita-documentation/) (Lita Foundation) and [Miden VM documentation](https://github.com/0xPolygonMiden/miden-vm) (Polygon), both free. For the "other systems" comparison.

4. **For memory checking specifically:** Blum, Evans, Gemmell, Kannan & Naor, [*Checking the Correctness of Memories*](https://www.cs.ubc.ca/~will/papers/memcheck.pdf) (free, co-author's academic page; original FOCS 1991 / Algorithmica 1994). Worth knowing this classical offline memory checking technique predates ZK entirely and was adapted into this context, rather than being invented for zkVMs.
---

## zkVM Architecture — the general pattern
A virtual machine whose execution can be **proven** via a STARK (or SNARK) — takes an arbitrary program (usually compiled from a real language like Rust) and generates a cryptographic proof that it executed correctly, without needing the verifier to re-run the program. This is the direct generalization of everything you've built: instead of a custom circuit for one specific computation, a zkVM proves *arbitrary* programs written against its instruction set.

## Instruction Sets
The VM's actual instruction set determines what programs it can run and how complex its AIR needs to be. Most modern zkVMs (RISC Zero, SP1, Valida, partially Jolt) target **RISC-V** specifically — see the RISC-V section below for why this choice matters so much.

## Virtual Machines (recap)
General concept — worth noting a zkVM's "virtual machine" role is identical in spirit to any VM (interprets/executes instructions against a defined state), with the addition that every execution step must also be **provable**.

## Execution Traces (recap, now at zkVM scale)
Same structure as your STARK/AIR notes, but now potentially **millions or billions of rows** — one row per instruction executed by a real program. This scale is exactly why continuations (below) become necessary rather than optional.

## Memory Checking — the genuinely crucial, newly-deep topic
Unlike Cairo's simpler write-once memory model (your Cairo notes), a general-purpose zkVM needs **random-access read/write memory** — and needs to *prove* that every memory read returns the most recently written value at that address, without the AIR needing to track full memory history explicitly (which would be prohibitively expensive). The standard technique — **offline memory checking** — works by: sorting all memory operations by address and time, then using a **permutation/multiset argument** (the same machinery from your PLONK/Lookup Arguments/Cairo notes, now applied to a third distinct problem) to check that this sorted view is internally consistent — i.e., that no read ever returns a stale or fabricated value. **This is a well-known classical technique (Blum et al.) predating ZK proofs entirely**, adapted into this context. Worth flagging directly: incomplete or subtly incorrect memory-consistency constraints are a **real, documented bug class** across nearly every major zkVM (connecting straight back to your Tier 2 zkVM vulnerability notes from the bug-bounty conversation) — this is exactly the kind of thing the Arguzz fuzzing paper found across RISC Zero, Nexus, Jolt, SP1, and others.

## CPU Constraints
The AIR constraints encoding "did the CPU correctly execute *this* instruction, given its opcode" — one constraint family per instruction type (add, multiply, branch, etc.), typically activated via a **selector-style mechanism** directly analogous to PLONK's selectors (your §18 notes), now applied to dispatch between different CPU instruction behaviors row by row rather than between different gate types.

## Register Constraints
A smaller-scale version of the memory-checking problem (above), applied specifically to the VM's register file — ensuring register reads/writes are consistent with actual register state across the execution trace.

## Lookup Arguments (recap, at zkVM scale)
zkVMs lean on lookup arguments heavily — validating opcodes against a legal instruction set, checking arithmetic results against precomputed tables (range checks, bitwise operations). **Jolt takes this to its logical extreme** (your ZK Languages notes) — proving nearly the entire CPU's behavior via lookups rather than hand-crafted per-instruction AIR constraints.

## AIR, STARKs (recap)
The proving backend for most major zkVMs — direct, large-scale application of your entire STARK/AIR/FRI cluster.

## Recursive Proving — why it's essential here specifically, not just nice-to-have
A single real program's execution trace can be **enormous** — proving it as one gigantic monolithic proof would require correspondingly enormous prover memory and time. Recursive proving lets you prove **smaller chunks** of execution separately, then recursively combine/verify those smaller proofs — directly connecting to your Recursive Proofs (§29, upcoming) and Halo/Cairo recursion notes.

## Continuations — the specific zkVM technique for unbounded execution
The concrete mechanism: split a long execution into multiple **segments** (fixed-size chunks of instructions), prove each segment's correctness **separately**, and use recursion to prove that **segment N+1's starting state correctly matches segment N's ending state** — chaining segments together via recursive verification rather than requiring one unbounded trace. This is precisely what lets a zkVM prove arbitrarily long programs (a program that runs for a billion cycles) without needing unbounded proving memory for a single giant trace — you pay roughly linear total cost, but in manageable, bounded-size pieces.

## RISC-V — why this specific choice, and its real tradeoff
Most modern zkVMs target RISC-V specifically because it's **open, simple, well-documented, and has mature compiler toolchains** (via LLVM/Rust) — meaning a zkVM team gets to support **real, existing programming languages** (Rust primarily) rather than forcing developers to learn a purpose-built circuit DSL. This is a major, deliberate adoption-driven tradeoff: **you accept somewhat more AIR complexity** (RISC-V wasn't designed with STARK-proving in mind, unlike Cairo's from-scratch STARK-friendly design) **in exchange for dramatically lower developer onboarding cost** — any existing Rust program becomes provable with comparatively little extra work. This is the exact same tradeoff axis from your Cairo notes' "Execution Model" section, now stated as the deliberate strategic choice most of the industry has made.

## zkVM Performance
The key practical metric most teams report: **proving overhead per RISC-V instruction** (often measured as "cycles per second" the zkVM can prove) — directly affected by continuation overhead, memory-checking efficiency, and how complex the AIR needs to be to correctly express RISC-V's full instruction semantics. This connects your entire month's work — field choice (Plonky3 notes), lookup argument efficiency (Lookup Arguments notes), and constraint degree (AIR notes) — into one concrete, comparable, real-world number teams actually compete on.

---

## Important Systems — a real architectural comparison

**RISC Zero**: STARK-based (their own prover lineage), targets RISC-V directly, uses continuations for long executions, general-purpose and widely adopted.

**SP1**: Built on **Plonky3** (your recent deep-dive) — RISC-V-targeting, from Succinct, emphasizing performance specifically via Plonky3's SIMD-friendly small field choices (Mersenne31/BabyBear-family).

**Jolt**: Recap — the lookup-singularity approach, RISC-V-targeting, but architecturally distinct from RISC Zero/SP1 in leaning on massive lookup arguments and sumcheck rather than traditional hand-crafted AIR per instruction.

**Valida**: Built by the Lita Foundation — notable for a **modular "chiplet" architecture**: separate, specialized proving components for different instruction types, similar in spirit to Cairo's builtins, designed with an emphasis on parallelizable proving. Worth knowing Valida's instruction set is RISC-V-*inspired* but not strictly identical, reflecting some willingness to deviate from strict RISC-V compatibility for provability gains — a middle ground between "strict RISC-V for compiler compatibility" and "fully custom ISA for STARK-friendliness."

**Miden**: Polygon's STARK-based VM, but taking the **opposite strategic choice** from RISC Zero/SP1/Valida: a **custom, non-RISC-V instruction set** specifically designed to be STARK-friendly from the ground up — the same philosophy as Cairo, prioritizing provability over existing-language compiler compatibility. Worth explicitly contrasting Miden and Cairo (the "custom ISA, STARK-friendly-first" camp) against RISC Zero/SP1/Valida (the "RISC-V, developer-adoption-first" camp) — this is the exact same strategic fork as your RISC-V tradeoff discussion above, now with named systems on each side.

---

## The big picture: two philosophies, one problem
Every zkVM is solving the same underlying problem (prove arbitrary program execution), but making a **deliberate strategic choice** along the same axis: **RISC Zero, SP1, Valida** prioritize compiler/language compatibility (accept more AIR complexity, gain real-world adoption ease) via RISC-V; **Cairo, Miden** prioritize proving efficiency (accept a custom DSL/toolchain, gain simpler, more STARK-friendly AIR) via purpose-built instruction sets; **Jolt** takes an orthogonal axis entirely — RISC-V-compatible *and* AIR-light, by leaning almost entirely on lookups instead. Understanding this three-way strategic landscape is more valuable than memorizing each system's individual spec sheet.

---

## Quick self-check before moving to Recursive Proofs
You're ready to move on once you can, without notes:
1. Explain offline memory checking precisely — what gets sorted, and what permutation-argument-style check confirms consistency
2. Explain continuations precisely — what gets proven about segment boundaries, and why this avoids needing unbounded single-proof memory
3. Explain the RISC-V vs. custom-ISA strategic tradeoff, and name which systems sit on each side
4. Explain where Jolt sits relative to this RISC-V/custom-ISA axis, and why it represents a genuinely orthogonal design choice
