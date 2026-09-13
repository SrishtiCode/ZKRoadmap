# Security Engineering (SOLID)

SOLID depth, matching your dev-first positioning: practical awareness of these attack classes so you'd catch them in your own code and could meaningfully discuss them, without this becoming a second full specialization. Several items here connect directly to real, recent, named incidents.

**Source:** Trail of Bits, [*Coordinated Disclosure of Vulnerabilities Affecting Girault, Bulletproofs, and PlonK*](https://blog.trailofbits.com/2022/04/13/part-1-coordinated-disclosure-of-vulnerabilities-affecting-girault-bulletproofs-and-plonk/) (free), the "Frozen Heart" series. Read alongside the follow-up posts on [Girault's proof of knowledge](https://blog.trailofbits.com/2022/04/14/the-frozen-heart-vulnerability-in-giraults-proof-of-knowledge/), [Bulletproofs](https://blog.trailofbits.com/2022/04/15/the-frozen-heart-vulnerability-in-bulletproofs/), and [PlonK](https://blog.trailofbits.com/2022/04/18/the-frozen-heart-vulnerability-in-plonk/). This is worth reading directly and specifically, since it's the exact vulnerability class behind the OtterSec zkVM disclosure from your earlier "what's still relevant" conversation (Jolt, Nexus, Cairo-M, Ceno, Expander, Binius64 all hit this same bug independently in 2026). Real, current, high-profile, and precisely on-topic.

---

## Trusted Setup Security, Toxic-Waste Destruction, Ceremony Design (recap)
Fully established from your Groth16 notes — the 1-of-n honesty model, the necessity of genuine destruction, and the practical concerns real ceremonies address: **contribution verification** (each participant must prove they correctly updated the SRS without revealing the new secret they contributed), and sometimes **randomness beacon** usage (public, unpredictable randomness sources) for extra entropy assurance.

## Randomness Generation
The general concern underlying everything: **weak or predictable randomness anywhere in the system breaks the corresponding security guarantee** — verifier challenges (if not properly Fiat-Shamir'd from a real hash), prover blinding factors (your Groth16 randomization notes), or ceremony contributions. Every negligible-probability soundness/ZK guarantee you've studied this month **assumes genuinely random inputs** — weak randomness silently invalidates the theoretical guarantee without necessarily producing any visible error.

## Transcript Security — the precise mechanics behind a real, current bug class
This is the one item in this list worth the most attention, because it's not theoretical: a Fiat-Shamir transcript must include **every value that could influence the proof**, hashed in a specific, unambiguous order, **before** deriving each corresponding challenge. **If any value affecting verification isn't absorbed into the transcript before its challenge is generated, a malicious prover can choose that value *after* seeing the challenge** — completely breaking soundness, since the "random" challenge is no longer independent of what the prover controls. **This is exactly the Frozen Heart vulnerability class**: a value affecting the verification equation wasn't absorbed into the transcript before the challenge was sampled, letting a prover see the challenge and choose the value afterward — and this precise bug was independently rediscovered by six different production zkVM teams in 2026, despite Trail of Bits documenting the identical pattern back in 2022. Worth internalizing as a genuine, real-world-common implementation mistake, not an edge case.

## Fiat-Shamir Security (recap, now grounded)
The Random Oracle Model security argument from your Crypto Foundations notes only holds if transcript construction is actually correct per the above — theoretical ROM security and practical transcript-completeness are two separate things that both need to be right.

## Soundness Errors (recap)
The quantifiable probability metrics from throughout your month (challenge space size, query count, negligible-probability bounds) — now understood as things that can be silently undermined by exactly the kind of transcript or randomness bugs described above, even when the underlying protocol's soundness *proof* is completely correct on paper.

## Subgroup Attacks, Small-Subgroup Attacks (recap)
Fully established from your Elliptic Curves notes — exploiting points outside the intended prime-order subgroup, mitigated by cofactor clearing and subgroup validation.

## Invalid-Curve Attacks
A related but distinct attack: submitting a point that doesn't actually satisfy the curve equation **at all** — effectively a point belonging to a **different, often deliberately weaker curve**. If an implementation doesn't explicitly validate that a deserialized point genuinely lies on the intended curve before using it in computation, this can leak information or produce exploitable results. This is a real, concrete, and commonly-skipped validation step — worth treating "validate the point is actually on the curve" as a non-negotiable check on any untrusted input, alongside subgroup checks.

## Malformed Proofs
The general category: proofs containing invalid or adversarially-crafted group elements, out-of-range field elements, or structurally malformed data. Real implementations need **explicit validation** (subgroup checks, curve-membership checks, range checks) on all untrusted proof data — never assume well-formed input, since a malicious prover controls every byte of the proof they submit.

## Commitment Binding (recap, with the practical risk made explicit)
From your Cryptographic Commitments notes — worth reinforcing that when binding is only **computational** (relying on a hardness assumption), the security guarantee is only as strong as that assumption continuing to hold. This connects directly to your post-quantum discussion from earlier — a computationally-binding, discrete-log-based commitment scheme's binding property specifically degrades if/when the underlying hardness assumption weakens.

## Implementation Vulnerabilities (umbrella)
The general category everything below falls under — bugs in how the correct cryptographic *design* gets translated into actual *code*, as distinct from flaws in the design itself.

## Side-Channel Attacks
Attacks exploiting information leaked through the **physical execution** of code — timing, power consumption, electromagnetic emissions — rather than breaking the underlying math directly. Particularly relevant for provers running on shared/untrusted infrastructure, or client-side proving on devices where an attacker might have physical or co-located access to observe execution characteristics.

## Timing Attacks — the most practically relevant side-channel for most ZK software
If field or curve arithmetic isn't implemented in **constant time** (execution time independent of the actual secret values being processed), an attacker measuring execution time variations can potentially extract information about secret witness values or private keys. **This connects directly back to your Elliptic Curves notes on Edwards curves** — their uniform addition formulas (no special-casing for doubling or identity) were specifically noted as being naturally more constant-time-friendly than Weierstrass form, which is exactly why timing-attack resistance is one real, concrete reason a system might choose Edwards curves for certain operations despite Weierstrass being more common overall.

## Serialization Attacks
Exploiting bugs in how proof or witness data gets serialized/deserialized — a genuinely mundane but common vulnerability class in **any** software handling untrusted serialized input, not unique to ZK. Concrete examples: a deserializer that fails to validate a deserialized curve point is actually on the curve (directly connecting to Invalid-Curve Attacks above), or integer overflow/parsing bugs in length-prefixed data formats. Worth treating deserialization of any untrusted proof data as a place requiring explicit, careful validation — precisely the same discipline as your Curve Serialization and Rust `CanonicalDeserialize` notes, now viewed through a security lens rather than a pure functionality lens.

---

## The real, current case study tying this whole section together
The 2026 OtterSec disclosure across six zkVMs is worth holding as your concrete anchor for this entire topic: **a categorical bug (Frozen Heart-style transcript incompleteness) was independently made by six different, well-resourced engineering teams**, years after the exact pattern was first documented. This tells you something genuinely important about ZK security: these bugs aren't exotic or requiring genius-level cryptographic insight to find — they're subtle, easy-to-miss implementation details that experienced teams still get wrong, which is exactly why systematic checklists (does every value affecting verification get absorbed into the transcript before its challenge? are all curve points validated?) matter more than raw cleverness.

---

## Quick self-check
You're ready to consider this section complete once you can, without notes:
1. Explain the Frozen Heart vulnerability class precisely: what goes wrong, and why six independent teams made the same mistake
2. Explain the distinction between invalid-curve attacks and small-subgroup attacks
3. Explain why Edwards curves' uniform addition formulas provide a genuine timing-attack advantage over Weierstrass form
4. Name three specific validation checks a careful implementation should perform on any untrusted proof/point data before using it
