# ZK Application Layer — Survey Notes

**Deliberately SKIM-level, per your depth guide — context for why the infrastructure matters, not deep study. Each entry maps to cryptographic building blocks you already understand, since at this level the value is recognizing "which of my tools does this application actually use," not learning new crypto.**

**No dedicated source needed** — this is genuinely a "know what exists and roughly how it works" list. If a specific application area becomes relevant to a company you're targeting, that's the trigger to go deeper on that one specific item, not this whole list.

---

| Application | What it actually is | Building blocks it uses (from your notes) |
|---|---|---|
| **ZK Identity** | Proving identity claims (age, citizenship, membership) without revealing the underlying document/data | Circuit proving a claim about private witness data — R1CS/Circom fundamentals |
| **ZK Authentication** | Proving you know a credential (password, key) without transmitting it | Sigma protocols, the exact Schnorr-style pattern from your Interactive Proofs notes |
| **ZK Credentials** | Verifiable, privacy-preserving credentials (e.g., "I have a valid degree") issued by a trusted party, provable without revealing the credential itself | Signature schemes + ZK proof of signature validity over private data |
| **ZK Voting** | Proving a vote was cast validly (by an eligible voter, exactly once) without revealing the vote's content or the voter's identity | Nullifier schemes (Merkle tree membership + a one-time-use tag), directly built from your Merkle/vector commitment and hash-function notes |
| **ZK Payments** | Transferring value while hiding amount/sender/recipient, while still proving the transaction is valid (no double-spend, correct balances) | Commitment schemes (hiding amounts) + nullifiers (preventing double-spend) + range proofs (via lookups, your §19 notes) |
| **ZK Rollups** | Already substantively covered — batching many transactions off-chain, proving the batch's validity with one succinct proof posted on-chain | Your entire STARK/SNARK/zkVM month, applied to a specific product category |
| **ZK Bridges** | Proving state/events from one blockchain to another without a trusted intermediary | Recursive proofs (proving a source chain's consensus/state validity) + your Recursive Proofs notes directly |
| **ZK Interoperability** | The broader category ZK Bridges belongs to — using proofs to let systems trust each other's state without a shared trusted party | Same building blocks as bridges, generalized |
| **ZKML** | Proving a machine learning model's inference was computed correctly (or that a model has certain properties) without revealing the model weights or input data | Arithmetic circuits over the model's actual computation graph — genuinely circuit-design-heavy, connects directly to your Arithmetic Circuits / Circuit Optimization notes, since ML operations (matrix multiplication, activations) need efficient circuit encodings |
| **Private DeFi** | Financial protocols (lending, trading) where transaction details stay private while still being verifiably valid | Combination of ZK Payments + smart contract logic proven correct via circuits |
| **Private Transactions** | The general category ZK Payments and Private DeFi both specialize | Same building blocks, generalized |
| **Proof of Reserves** | An exchange/custodian proving they hold sufficient assets to cover liabilities, without revealing individual account balances | Merkle tree commitments to a full balance sheet + range proofs/sum proofs over private leaf values |
| **zkTLS** | Proving the content of a TLS (HTTPS) session occurred as claimed, without revealing the full session or requiring the server's cooperation | Circuit proving correct TLS protocol execution — a genuinely complex, "prove an existing protocol's correct execution" circuit design problem |
| **zkEmail** | Proving properties of a real, DKIM-signed email (e.g., "this email came from this domain and says X") without revealing the full email content | Circuit verifying a DKIM signature (RSA or similar) over private email content — directly connects to your Number Theory notes on modular exponentiation, since RSA verification inside a circuit is exactly this kind of expensive-operation-in-circuit problem your Hashes/Custom Gates notes prepared you to reason about |
| **Private Reputation** | Proving reputation-related claims (credit score above X, positive review history) without revealing the underlying data | Range proofs/lookups over private aggregate data, similar building blocks to ZK Credentials |

---

## The one real insight worth taking from this list
Notice that **every single application here is built from the same small set of primitives you've spent the whole month mastering**: commitment schemes (hiding), Merkle/vector commitments (membership and nullifiers), range proofs via lookups, arithmetic circuits encoding some real-world computation, and recursive proofs for cross-system trust. The "application layer" isn't a new body of cryptography — it's **product design and circuit engineering** applied on top of the exact toolkit this entire roadmap built. This is genuinely reassuring: you don't need a fifth month of new theory to eventually work on any of these; you need domain-specific circuit design experience layered onto what you already have.

---

## Quick self-check
You're ready to consider this section complete once you can, without notes:
1. Identify which 2-3 cryptographic building blocks from your month recur across the most application entries in this table
2. Explain what a "nullifier" is and why it's the key mechanism behind both ZK Voting and ZK Payments preventing double-use
3. Explain why zkTLS and zkEmail are considered "circuit-design-heavy" rather than requiring new cryptographic theory
4. Pick one application area you find personally interesting and identify which of your Week 1-4 deep-dive topics would matter most if you specialized in it
