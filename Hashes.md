# Hashes for ZK (SOLID)

**Sources:**

1. Grassi, Khovratovich, Rechberger, Roy & Schofnegger, [*Poseidon: A New Hash Function for Zero-Knowledge Proof Systems*](https://eprint.iacr.org/2019/458) (free, IACR ePrint). For the design rationale behind the algebraic hash approach.

2. Grassi, Khovratovich & Schofnegger, [*Poseidon2: A Faster Version of the Poseidon Hash Function*](https://eprint.iacr.org/2023/323) (free, IACR ePrint). For the specific improvements over the original.

3. For comparing Rescue, Griffin, Anemoi, and MiMC side by side rather than reading five separate papers cover to cover: [*Gotta Hash 'Em All! Speeding Up Hash Functions for Zero-Knowledge Proof Applications*](https://arxiv.org/pdf/2501.18780) (free, arXiv survey/benchmark paper), and TACEO's practitioner writeup [*Which ZK Hash Should You Use?*](https://core.taceo.io/articles/how-to-choose-your-zk-friendly-hash-function/) (free blog post, includes decision trees for choosing based on proof system and cost metric).

---

## The two questions this topic is actually built around

### Why is SHA-256 expensive inside a circuit?

SHA-256's internal operations — **bitwise AND/OR/XOR, bit rotations, modular addition of 32-bit words** — don't map efficiently onto arithmetic circuits over a large prime field, because **XOR (and other bitwise operations) aren't native field operations**. A ZK circuit's native operations are field addition and multiplication over 𝔽_p; XOR has no direct algebraic expression in terms of + and ×.

To express a single bitwise XOR of two field elements as R1CS/AIR constraints, you have to: **decompose** each element into its individual bits (itself costing multiple constraints per element — connecting to your Number Theory notes on binary representation), implement the bitwise XOR logic **bit by bit** as constraints, then **recompose** the result back into a field element. A single 32-bit XOR might require 32+ constraints just for the decomposition/recomposition overhead, before the actual bitwise logic constraints are even added. SHA-256 runs many rounds, each packed with exactly this kind of bitwise operation — the cumulative effect is **thousands of constraints per single hash invocation**, which is enormously expensive when hashing happens constantly (every Merkle tree operation, in your own STARK prover, needs this).

### Why are Poseidon/Poseidon2 useful?

Poseidon is designed **from the ground up using operations native to field arithmetic** — addition and multiplication directly, with a **low-degree power function** (typically x⁵) as its nonlinear "S-box" component, instead of bitwise operations. The choice of exponent (5, or another small value) is deliberate: it needs to be **coprime to p−1** (connecting to your Number Theory / Euler Phi Function notes) so the function is a genuine bijection/permutation, while staying **low-degree enough to be cheap to constrain** — computing x⁵ as a constraint is just **2–3 multiplication constraints** (x², x⁴=x²·x², x⁵=x⁴·x), not 32 bit-decomposition constraints.

This native-field-operation design is what **collapses a hash invocation from thousands of constraints (SHA-256) down to tens or low hundreds (Poseidon)** — a massive, directly measurable efficiency gain for any ZK application that needs hashing, which is nearly all of them.

---

## The rest of the list, briefly

**SHA-2, SHA-3/Keccak**: bit-oriented hashes, not designed for circuit efficiency — Keccak specifically matters because it's Ethereum's native hash (`keccak256`), so any zkEVM proving real Ethereum execution has to pay this exact in-circuit cost somewhere, which is a large, well-known source of proving overhead in zkEVM systems.

**Poseidon2**: an updated, more efficient version of Poseidon — improved round/linear-layer structure reducing constraint count further. Increasingly the default choice in modern systems (as noted in your Plonky3 notes, where it's co-designed alongside the small-field SIMD-friendly arithmetic).

**Rescue / Rescue-Prime**: alternates between low-degree (x⁵-style) and high-degree (inverse, x^(1/5)-style) S-boxes across rounds — a different security/efficiency tradeoff design than Poseidon's approach. Rescue-Prime is a refined, optimized version of the original Rescue construction.

**Griffin**: a newer algebraic hash, designed to push efficiency further than Poseidon for specific field sizes and proof systems — part of the same "algebraic hash" family, optimizing the same underlying tradeoff (constraint count vs. security margin) differently.

**Anemoi**: notable for a genuinely different design goal — most algebraic hashes trade native (non-circuit) computation speed for circuit-friendliness; Anemoi tries to be efficient in **both** contexts simultaneously, useful when the same hash needs to run fast both inside a circuit and in ordinary (non-proving) code.

**MiMC**: one of the earliest algebraic/ZK-friendly hash designs, predating Poseidon — a simple low-degree round function repeated many times, following a "minimal multiplicative complexity" design philosophy. Largely superseded by Poseidon-family hashes in new systems for efficiency reasons, but historically foundational — worth knowing as the design lineage's starting point.

**Algebraic Hashes**: the general category — Poseidon, Rescue, Griffin, Anemoi, and MiMC all belong here, defined by being built from native field operations (+ and low-degree ×) rather than bitwise operations, in direct contrast to SHA-2/Keccak's bit-oriented design.

**ZK-friendly Hashes**: the umbrella term for this entire category — hash functions specifically designed or chosen to minimize constraint count when represented inside a ZK circuit, as opposed to hash functions chosen for other properties (standardization, hardware acceleration, non-ZK use cases) the way SHA-2/Keccak were.

---

## Quick self-check
You're ready to move on once you can, without notes:
1. Explain precisely why a single bitwise XOR is expensive to express as a field-arithmetic constraint, and roughly how many constraints the decomposition overhead costs
2. Explain why Poseidon's S-box exponent needs to be both coprime to p−1 and low-degree, and what each requirement is protecting against/optimizing for
3. Explain why Keccak's in-circuit cost is a genuinely significant practical problem specifically for zkEVMs, as opposed to ZK applications generally
4. Name one algebraic hash that trades off differently from Poseidon (either in security-margin design or in optimizing for non-circuit speed too), and explain the tradeoff
