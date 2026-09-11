# PLONK — ZK Context Notes

**Full depth treatment — this is your Week 2 target, and you're arriving with permutation arguments already deeply understood from earlier quizzes, KZG already implemented, and Fiat-Shamir already solid. This section is mainly about assembling those pieces into PLONK's specific protocol shape, plus genuinely new material: the table-based arithmetization model.**

**Sources:**
1. **The PLONK paper itself** (Gabizon, Williamson, Ciobotaru) — for the grand product argument and quotient polynomial mechanics precisely.
2. **The Halo2 Book's "Concepts" section** — genuinely the right source specifically for the column/selector/table terminology ("PLONKish arithmetization" as a term postdates the original PLONK paper and was popularized by Halo2's documentation). Use this specifically for advice/fixed/instance columns and selectors.

---

## PLONK — the high-level assembly
Universal setup (§17, now understood) + custom gates + permutation argument (already deeply understood from your earlier quizzes) + KZG polynomial commitments + Fiat-Shamir = a practical, universal SNARK. This section is about how these pieces fit together into one concrete protocol.

## PLONKish Arithmetization — the table model
The circuit is represented as a **2D table**: rows correspond to execution steps or gate instances, columns correspond to different kinds of values (witness data, constants, public inputs). Constraints are polynomial equations checked **across rows and columns** of this table. This is a genuinely different mental model from R1CS's matrix-vector framing — instead of thinking "one constraint = one row of A,B,C," you think "one gate type = one polynomial equation that must hold at every row where that gate is active." This table model is what "PLONKish" refers to, and it's shared (with variations) across PLONK, Halo2, and most modern systems you'll study this month.

## Advice Columns
Columns holding **prover-supplied witness values** — filled in during proving, private, the actual "variable" data flowing through the circuit. Directly analogous to your witness vector w, just organized as a column in the table instead of a flat vector.

## Fixed Columns
Columns holding **circuit-designer-chosen constants**, fixed at circuit-design time — not filled in per-proof, but baked into the circuit's definition itself. Selector values (below) and lookup table contents typically live in fixed columns.

## Instance Columns
Columns holding **public inputs** — known to both prover and verifier, analogous to the public-input portion of your R1CS witness vector z, again reorganized into the table structure.

## Selectors — the mechanism enabling custom gates
Special fixed-column values (often simply 0 or 1) that **"turn on" or "turn off" a specific constraint type for a specific row**. This is the actual mechanism behind custom gates from your Arithmetic Circuits notes: a single circuit definition can include several different gate types (a basic addition gate, a multiplication gate, a specialized hash-round gate), each with its own selector column — for any given row, exactly the constraints whose selector is "on" actually apply. This is how PLONK avoids needing a separate circuit structure for every different operation type — one shared table, with selectors routing which constraints matter where.

## Copy Constraints & Permutation Arguments (recap, now in full protocol context)
Already deeply established from your earlier quizzes: copy constraints assert that specific cells (across possibly different rows/columns) must hold equal values, verified via a permutation argument rather than checking each pairing individually. This is where that earlier deep-dive pays off directly — you already understand *why* this works; this section is about seeing it embedded in PLONK's actual pipeline.

## Grand Product — the concrete mechanism, precisely
The actual polynomial **Z(X)** constructed as a **running product**, used to verify the permutation claim. Concretely: Z(X) is built so that Z at each point accumulates a ratio of products (roughly, ∏(value + challenge·position) over the claimed permutation, divided by the same product over the original ordering) — and if the permutation is genuinely correct, this running product telescopes to 1 by the end; if it's wrong, it doesn't, except with negligible probability over the random challenge. Z(X) itself gets **committed to** (via KZG) and its correctness gets folded into the overall constraint checking — this is the concrete polynomial object underlying the abstract "grand product check" from your earlier Permutations quiz.

## Quotient Polynomial — PLONK's version, distinct from QAP's h(X)
Worth being precise about a real difference from Groth16: PLONK's quotient polynomial is a **single combined polynomial checking multiple different constraint families simultaneously** — the gate constraints (custom gate equations) *and* the permutation/copy constraints (the grand product check) *and* (if used) lookup constraints — all combined together using a **random linear combination** (the exact Schwartz-Zippel-style trick from your Linear Combinations notes: combine several different equality claims into one using random challenge-weighted coefficients, since a random combination catches any individual inequality with overwhelming probability). This is conceptually different from QAP's single h(X), which only ever checked one thing (R1CS satisfiability) — PLONK's quotient polynomial is doing the work of several different QAP-style checks bundled into one, via randomized combination.

## Polynomial Commitments, KZG, Fiat-Shamir (recap)
Already fully established — PLONK's specific instantiation choice is typically KZG (though IPA and FRI-based variants exist too, per your Cryptographic Commitments notes), with Fiat-Shamir converting the whole interactive Polynomial IOP into a non-interactive proof.

## Custom Gates (recap, now precisely mechanized)
From your Arithmetic Circuits notes, now fully concrete: a custom gate is a polynomial constraint equation, activated at specific rows via its selector column, letting a single circuit efficiently express specialized repeated operations (a full hash-function round, a range check) without decomposing them into many basic R1CS-style multiplication constraints.

## Lookups — brief intro here, full depth scheduled at §19
A constraint type checking that a value **belongs to some predefined table** (e.g., "this byte value is in the range 0–255," or "this pair of inputs/outputs matches a valid entry in this lookup table") — implemented using machinery closely related to the permutation argument (multiset equality checks via similar grand-product-style techniques). You have a dedicated deep-dive for this at §19 — for now, just note it as another constraint family that gets folded into PLONK's combined quotient polynomial alongside gate and permutation constraints.

---

## Quick self-check before moving to Lookup Arguments
You're ready to move on once you can, without notes:
1. Explain the table model (advice/fixed/instance columns) and how it differs conceptually from R1CS's matrix-vector framing
2. Explain what a selector does and how it enables multiple gate types to coexist in one circuit definition
3. Explain, at the level of "what it's accumulating and why it telescopes to 1 when correct," what the grand product polynomial Z(X) is doing
4. Explain why PLONK's quotient polynomial is doing more work than QAP's h(X), and what technique lets multiple constraint families get combined into one polynomial check
