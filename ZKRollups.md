# ZK Rollups (SOLID)

**Sources:**

1. Vitalik Buterin, [*A Rollup-Centric Ethereum Roadmap*](https://ethereum-magicians.org/t/a-rollup-centric-ethereum-roadmap/4698) (free, 2020). For the original architectural rationale: why rollups, why this structure. Note: Vitalik publicly reconsidered parts of this roadmap in February 2026, citing slower-than-expected L2 decentralization; treat this piece as the foundational design rationale, not as an up-to-date statement of Ethereum's current strategy, and look up his 2026 remarks for the current state of the debate.

2. [StarkNet documentation](https://docs.starknet.io/) and [zkSync documentation](https://docs.zksync.io/) (both free). Practical, concrete descriptions of real production rollup architecture.

3. [L2beat](https://l2beat.com/) (free). A genuinely useful resource for comparing real, live rollups against each other on exactly these dimensions (proving system, data availability choice, decentralization status). Worth bookmarking as an ongoing reference, not just a one-time read.

---

## State Transition Proofs — the actual thing being proven
A rollup's core cryptographic claim: **"executing this batch of transactions against the previous state correctly produces this new state."** This is precisely your R1CS/AIR satisfiability framing (your whole month's foundational work), applied to one specific computation: blockchain state transition. Everything else in this list is architecture built around producing and using this one proof.

## State Roots
A compact commitment (typically a Merkle root, your Vector Commitments notes) to the entire rollup's account/storage state. The state transition proof effectively proves: "given old_state_root, applying this batch of transactions correctly yields new_state_root" — the roots themselves are what gets posted on-chain, not the full state.

## Execution Traces (recap)
Fully established — the rollup's sequencer executes the batch of transactions, producing exactly the kind of execution trace your STARK/zkVM notes are built around, which then gets proven.

## Sequencers
The party (or parties) that **orders and executes** incoming transactions, producing the execution trace and proposed new state — distinct from the **prover** role (below). Sequencer centralization is a genuine, actively-discussed real-world concern in the rollup space (most current rollups have a single, centralized sequencer, which is a known limitation actively being worked on across the industry).

## Provers
The party (or parties) that takes the sequencer's execution trace and **generates the actual validity proof** — this is the role your entire month of study prepares you to work on directly. Proving can be centralized or (increasingly) distributed/decentralized across multiple provers competing or collaborating — connecting to your Parallel Proving and Prover Engineering notes on why proving is often the actual bottleneck resource in these systems.

## Verifiers
On the L1 side: a **smart contract** that checks the submitted validity proof — this is where your Groth16/PLONK/STARK verification-complexity notes become directly, concretely relevant: L1 verification cost (gas cost, in Ethereum's case) is a real, economically significant constraint, which is exactly why succinct proof size and fast verification (the entire point of your SNARK/STARK study) matter commercially, not just theoretically.

## L1 Verification
The actual on-chain step: the L1 verifier contract checks the proof and, if valid, accepts the new state root as canonical. This is the trust anchor of the whole system — L1 verification is what lets a rollup inherit L1's security guarantees rather than requiring its own independent trust assumptions.

## Data Availability (DA)
A genuinely important, distinct concern from validity: even with a valid proof that a state transition was computed correctly, **someone needs access to the underlying transaction data** to reconstruct/verify state independently or to challenge/recover from a sequencer failure. Different rollups make different DA choices — posting full data on L1 (expensive, most secure), using a separate DA layer (cheaper, different trust assumptions), or various hybrid approaches. This is a major, real architectural decision point distinguishing different production rollups from each other, independent of which proving system they use.

## Validity Proofs
The general term for what your whole month has been building toward — a proof that a state transition is *valid* (correctly computed), as opposed to "optimistic rollups" (a different, non-ZK architecture that assumes validity and only proves fraud if challenged). ZK rollups specifically use validity proofs; this is the actual name for what you've spent months studying, now in its product context.

## Recursive Aggregation (recap, in rollup context)
Directly your Recursive Proofs and Folding Schemes notes, applied concretely: rather than proving an enormous batch of transactions as one giant proof, recursive aggregation lets a rollup combine many smaller proofs (or process transactions incrementally via IVC/folding) into one final, compact proof — exactly the continuations/IVC pattern from your zkVM notes, now understood as literally how real production rollups handle scale.

## Batch Proving
The practical decision of **how many transactions** get grouped into one proof before submission — a real tradeoff between proving latency (smaller batches, faster individual proofs, more frequent L1 submissions) and proving efficiency (larger batches, better amortized cost per transaction, but longer latency before finality).

## Compression
Reducing the size of the data that needs to be posted (for data availability) or proven — techniques include only posting the minimal data needed to reconstruct state changes (rather than full transaction data), and various encoding optimizations. Directly connects to your Serialization notes — efficient encoding of on-chain data is a real, measurable cost driver given L1 data posting costs.

## Settlement
The final step: once the validity proof is verified on L1, the new state root becomes **canonical and final** from L1's perspective — this is what gives a rollup its security guarantee (finality inherited from L1) as opposed to a sidechain or other architecture with independent, weaker trust assumptions.

---

## The full picture: your entire month, as one product
Sequencer orders transactions → produces execution trace → **prover** (your STARK/SNARK/zkVM expertise) generates a validity proof of correct state transition → proof gets **recursively aggregated/compressed** if needed → submitted with **data** (per the rollup's DA choice) to **L1**, where a **verifier contract** checks the proof cheaply (thanks to succinctness) → new **state root** becomes **settled**. Every single stage in this pipeline maps to something you've studied in depth this month — this is, quite literally, the assembled product your entire roadmap has been preparing you to contribute to.

---

## Quick self-check — closing out the full 44-topic list
You're ready to consider your full topic-depth audit complete once you can, without notes:
1. Explain the distinct roles of sequencer, prover, and verifier, and why proving is often the actual bottleneck resource
2. Explain why data availability is a genuinely separate concern from validity, even when a valid proof already exists
3. Explain how recursive aggregation/IVC (your Recursive Proofs and zkVM continuations knowledge) applies concretely to handling large transaction batches
4. Explain why L1 verification cost being cheap (thanks to succinct proofs) is a real economic constraint, not just a theoretical nicety
