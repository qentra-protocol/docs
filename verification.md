# Verify a checkable draw

A prior Qentra commitment precedes the request. Fixed application inputs and the user's 32-byte contribution bind a committed slot. The program checks the reveal and publishes the result. Verification has distinct layers:

1. `verifyProtocolRecord(record)` checks schema-4 fulfilled state, SHA-256 input digest, the `numHashes` (1–256) commitment chain, domain-separated output and application mapping. It needs no wallet or network.
2. `client.verify(record)` independently reopens the network/program/sequence identity from RPC, recomputes proof and compares authoritative record fields and mapping.
3. `client.verifyPayment(record)` checks the successful same-transaction payment and proof request. `paymentVerified`/`feePaid` distinguish payment evidence from randomness checks.

Output formula: `SHA256(UTF8('qentra-solana-v1') || requestAddressBytes || userContributionBytes || providerContributionBytes || inputDigestBytes)`. Fixed inputs are serialized in their saved JSON order before hashing. Preserve the exact exported input object rather than rebuilding it with a different property order. Mapping uses core's `qentra-sha256-v1` deterministic rejection sampling; expansion is not additional entropy.

Inspect `commitmentTx`, `requestTx`, `revealTx` through [Solana Explorer](https://explorer.solana.com/). The SDK associates prior commitments through configuration history and checks the chain lifecycle. An offline proof alone cannot establish transaction inclusion, earlier publication ordering or payment; use RPC comparison and transaction links for those layers.

The actual no-rent mainnet request 2 produced range result 40, with 500000 lamports service fee, 5000 transaction fee and zero per-draw rent. Historical request 1 produced result 28 and remains account-compatible. These are existing proofs, not new requests created by the examples. Qentra commitments and their output integrity are checkable; source physics certification is a separate trust property.
