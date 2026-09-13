# Halo / Halo2 (DEEP)

**Sources:**

1. The Halo2 Book, [*Concepts*](https://zcash.github.io/halo2/concepts.html) (free, already recommended). The single best source for this entire section. Written by the team that built it, it directly defines Regions, Gates, Chips, and the column/selector model in exactly the terms you'll use when reading real Halo2 circuit code.

2. Bowe, Grigg & Hopwood, [*Halo: Recursive Proof Composition without a Trusted Setup*](https://eprint.iacr.org/2019/1021) (free, IACR ePrint). Specifically for the recursion/accumulation mechanism. This is where "Halo" as a technique (separate from Halo2 the production system) was introduced, worth reading specifically for the accumulation idea, since Halo2's book assumes some of this background.

---

## Halo vs. Halo2 — a naming distinction worth being precise about
**"Halo"** refers to the original **recursion technique** introduced by Bowe, Grigg, and Hopwood — a way to achieve efficient recursive proof composition without expensive in-circuit pairing operations, using an accumulation scheme (below). **"Halo2"** is the production proving system (built by the Electric Coin Company for Zcash, now widely used elsewhere including Scroll's zkEVM) that combines the Halo recursion technique with a PLONKish arithmetization and (typically) an IPA-based polynomial commitment scheme. People often say "Halo2" when they mean the whole system, and "Halo" specifically when discussing the recursion trick — worth keeping these distinct since papers are precise about which one they mean.

## PLONKish Circuits, Advice/Fixed/Instance Columns, Selectors (recap)
Fully established in your PLONK notes — Halo2 uses the identical table-based arithmetization model. No new concepts here, just confirming Halo2 is a concrete implementation of the PLONKish pattern you already understand.

## Regions — Halo2-specific circuit layout concept
A genuinely new Halo2-specific idea: a **region** is a logical grouping of cells (across rows and columns) that belong together as one "unit" of circuit logic — e.g., all the cells involved in one hash-function invocation, grouped as a single region, even though the underlying table doesn't inherently know about this grouping. Regions are a **circuit-authoring abstraction**: they let circuit designers write reusable, composable pieces of circuit logic ("chips," in Halo2's terminology) without manually tracking exact row numbers — the Halo2 framework handles region placement and layout automatically. This is a practical engineering concept, not a cryptographic one — but it's essential for actually writing real Halo2 circuits rather than just understanding the underlying math.

## Gates — Halo2-specific terminology
In Halo2 specifically, a "gate" refers to a **polynomial constraint equation** defined over the cells in a region (or more generally, over specific column/row combinations), activated via a selector — directly the same concept as "custom gates" from your PLONK notes, just using Halo2's specific terminology and region-based organization.

## Constraints & Equality Constraints
General "constraints" are the polynomial equations gates enforce. **Equality constraints** are Halo2's specific term for what your PLONK notes called "copy constraints" — asserting two cells (possibly in different regions, rows, or columns) must hold equal values, enforced via the permutation argument. Same underlying concept, Halo2-specific vocabulary — worth knowing both terms since you'll see "copy constraint" in the PLONK paper and "equality constraint" in Halo2 documentation referring to the identical idea.

## Permutation Arguments, Lookups (recap)
Fully established from your PLONK and Lookup Arguments notes — Halo2 uses the same underlying techniques, just within its own column/region framework.

## Polynomial Commitments, KZG, IPA — Halo2's real choice point
Halo2 is notable for being **commitment-scheme-agnostic** in principle, but its most distinctive, original configuration uses **IPA** (Inner Product Argument, from your Cryptographic Commitments notes) rather than KZG — precisely because IPA is **transparent** (no trusted setup), which pairs naturally with Halo2's recursion-based approach to avoiding trusted setup entirely (as opposed to PLONK's typical KZG-based, universal-but-still-trusted setup). Some Halo2 deployments (including some production zkEVMs) do use KZG-based variants instead, trading transparency for smaller proofs — worth knowing both configurations exist rather than assuming Halo2 always means IPA.

## Recursive Proofs — brief intro here, full depth at §29
The general idea (your dedicated deep-dive is coming at §29): a proof that itself verifies the correctness of another proof, enabling proof composition and, ultimately, unbounded computation verified with bounded proof size. Halo's specific contribution to this space is making recursion **efficient without pairings** — relevant now because pairing operations are expensive to perform *inside* a circuit (you'd be simulating elliptic curve arithmetic using arithmetic circuit constraints, which is costly), and Halo's accumulation trick (below) sidesteps needing to do this.

## Accumulation — the actual mechanism behind "Halo"
This is the core idea worth understanding now, even before your full Recursive Proofs deep-dive: instead of a recursive proof **fully verifying** the previous proof inside the circuit (expensive — would require simulating pairing/IPA verification arithmetic in-circuit), the circuit instead **accumulates** a claim to be checked later, combining it with the current proof's own claims into a single, larger "accumulator" instance. The actual expensive verification work gets **deferred** — potentially all the way to the very end of a long chain of recursive proofs — rather than being repeated fully at every single step. This deferred-checking pattern is precisely what makes Halo-style recursion efficient: you pay the expensive verification cost once, at the end, instead of once per recursive step.

## Halo Recursion — putting it together
The specific mechanism: at each recursive step, instead of fully verifying the prior proof, the prover produces a new accumulator that **folds together** the prior accumulator's claim with the new step's claim — using IPA's specific algebraic structure to make this folding operation cheap (crucially, cheaper than a full IPA verification would be). Only at the very end does anyone need to perform one full, expensive verification of the final accumulated claim — checking, in effect, that the entire chain was valid, without ever having paid full verification cost at each individual step. This is conceptually a direct ancestor of the folding schemes (Nova, SuperNova — your upcoming §30) you'll study later — "accumulate now, verify fully later" is the same underlying philosophy both approaches share, even though the specific algebraic machinery differs.

---

## Quick self-check before moving to STARKs
You're ready to move on once you can, without notes:
1. Explain the distinction between "Halo" (the technique) and "Halo2" (the system), and why this distinction is worth keeping precise
2. Explain what a Region is in Halo2 and why it's a circuit-authoring convenience rather than a cryptographic concept
3. Explain why Halo2's IPA-based configuration pairs naturally with its recursion goals, in a way KZG wouldn't as cleanly
4. Explain the core idea of accumulation — what gets deferred, and why deferring it makes recursive proof composition efficient
