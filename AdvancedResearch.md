# Advanced Research Areas — ZK Context Notes
**The final section of your full 44-topic list.**

**Deliberately SKIM/ongoing-awareness level, per your depth guide — this is genuinely research-frontier material where "read a survey paper every few months" is the right cadence, not a one-time deep study. Most items are already covered; a handful of genuinely new pieces close out the whole curriculum.**

**No single source** — for staying current here, the right habit is periodically checking eprint.iacr.org's recent postings, ZK-focused conference proceedings (Eurocrypt, Crypto, real-world crypto), and following the specific companies/researchers whose work you've studied this month (Thaler, Setty, Boneh, the Succinct/RISC Zero/StarkWare research teams).

---

## Already covered — final recap

| Term | Where |
|---|---|
| **Proof aggregation, Recursive SNARKs, Folding, IVC** | Recursive Proofs (§29) and Folding Schemes (§30) notes |
| **zkVMs** | zkVMs (§28) notes, full depth |
| **Lookup arguments** | Lookup Arguments (§19) notes, full depth |
| **GPU proving, Parallel proving** | Prover Engineering and ZK Optimization notes |
| **Transparent SNARKs** | STARKs, FRI, Bulletproofs/IPA, Brakedown/Orion (Modern Proof Systems survey) — the whole transparent-setup family |

## zkML (recap, brief)
Touched in your Application Layer survey — proving ML inference correctness via arithmetic circuits over the model's computation graph. Genuinely active research area (efficient circuit encodings for matrix multiplication and nonlinear activations at ML scale is a real, unsolved-at-scale problem) — worth knowing it's a distinct specialization if it ever becomes personally interesting, not something to chase now.

---

## Genuinely new — closing pieces

## Post-Quantum ZK, Lattice-Based ZK — tying together your earlier thread
You already encountered the urgency here directly in your "what's still relevant" conversation: LatticeJolt (released the day before your "today," per that search) shifts Jolt's cryptographic foundation from elliptic curves to **lattices**, targeting security based on the **Module-SIS** assumption — the same assumption underlying ML-KEM (the NIST-standardized post-quantum key exchange). **Lattice-based ZK** is the broader research category this belongs to: building proof systems whose security rests on lattice problems (believed hard even for quantum computers) rather than discrete-log/pairing assumptions (broken by Shor's algorithm). Connecting back to your Advanced Cryptography notes: this is a direct, practical response to exactly the DLOG-family vulnerability you identified there — lattice-based constructions sidestep that vulnerability class entirely by resting on a structurally different hardness assumption.

## Hash-Based ZK
Worth naming explicitly as a category, even though you've been using it the whole month: **your entire STARK/FRI cluster is hash-based ZK** — security resting on hash function collision resistance rather than number-theoretic assumptions. This is *already* one of the more quantum-resistant proving approaches available (hash functions degrade only quantitatively under quantum attack — larger digests, more rounds needed — rather than collapsing entirely the way discrete-log-based schemes do). Worth the explicit reframe: you didn't skip hash-based ZK as an advanced research topic, you built one of its flagship instances from scratch back in Week 1.

## Proof-Carrying Data (PCD) — genuinely new
A generalization of IVC worth knowing precisely: IVC (your Recursive Proofs notes) handles a **linear chain** of computation steps, each depending only on the previous one. **PCD generalizes this to arbitrary directed-acyclic-graph (DAG) structured computation** — proofs can carry "data" (attestations) through a more general computation graph, where a given step might depend on **multiple** prior proofs merging together, not just one linear predecessor. This matters for real distributed/multi-party computation scenarios — e.g., proving properties of a computation that genuinely branches and merges (multiple parties contributing independently, then combining), rather than a strictly sequential chain. Worth knowing PCD as "IVC's generalization for non-linear computation graphs" — the conceptual relationship is precise and worth remembering even without full construction-level depth.

## Distributed Proving
Beyond single-machine parallel proving (your Prover Engineering notes), **distributed proving** specifically means splitting proof generation across **multiple separate machines** (not just multiple cores/GPUs on one machine) — relevant for extremely large circuits/traces where even a single powerful machine's memory and compute are insufficient. Real production systems increasingly need this for handling genuinely large-scale computation (e.g., proving an entire Ethereum block's execution), connecting directly to the continuations/segmentation patterns from your zkVM notes, now applied at the infrastructure level (different segments proven on different physical machines, not just different threads).

## ZK Hardware Acceleration
Beyond GPU proving specifically (already covered), the broader category includes **FPGAs** (reconfigurable hardware, a middle ground between general-purpose CPU/GPU and fully custom silicon) and **ASICs** (application-specific integrated circuits — custom silicon designed specifically for ZK operations like MSM and NTT, the fastest possible but most expensive/inflexible option). This is a genuinely active, well-funded research and industry area — multiple companies are actively building specialized ZK hardware, following the same "purpose-built silicon beats general-purpose hardware for high-value, well-defined repeated operations" pattern that produced Bitcoin ASIC mining hardware and GPU-specific AI accelerators. Worth knowing this exists as a career-adjacent specialization (hardware-focused ZK engineering) distinct from, but connected to, the software prover engineering your month has focused on.

---

## Closing the entire 44-topic curriculum
This is the last section of the full list you started with back at the beginning of this conversation. Notice, looking back across everything: **the research frontier (this section) and the deferred theory (Advanced Cryptography, PCP theorem) both turned out to be smaller than they looked from the outside** — because the core Tier 1-3 material you built in real depth (polynomial commitments, IOPs, sumcheck, recursion, coding theory) is the actual substance underneath almost every "advanced" or "research" topic name. That's not luck — it's what a well-sequenced curriculum is supposed to produce: depth in the right foundational places makes everything built on top of it legible, rather than needing to be learned as a separate, disconnected body of facts.

---

## Final quick self-check
1. Explain the precise relationship between IVC and PCD — what does PCD generalize, and why does that generalization matter for distributed computation
2. Explain why hash-based ZK (STARKs/FRI) degrades only quantitatively under quantum attack, while discrete-log-based schemes (Groth16) can collapse entirely
3. Explain the distinction between distributed proving and the parallel proving (SIMD/multi-core) you studied earlier
4. Name the three hardware options (GPU, FPGA, ASIC) in order of increasing specialization/performance and decreasing flexibility
