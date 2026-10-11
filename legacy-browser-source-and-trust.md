> Historical browser adapter only (`@qentra/sdk/browser`, schema 1). For native Solana requests use [Quickstart](quickstart.md).

# Entropy origin, verification and delivery

Qentra Protocol separates quantum-origin entropy, a checkable generation record, reproducible application mapping and onchain delivery. The current source-development adapter is `BrowserEntropyAdapter`, using Web Crypto's `getRandomValues` in the executing environment.

The adapter explicitly records `browser-cryptographic` source mode. It does not supply a quantum-source certificate. The record's `verification: not-applicable` and `delivery: not-requested` describe this adapter's boundaries. Import replay checks consistency of the recorded input, bytes and mapping. It does not independently authenticate where the entropy came from or settle an onchain transaction.

Token request fees and provider compensation are protocol integration concerns outside this initial browser adapter. A future source or settlement adapter needs separately reviewed evidence and delivery contracts; do not infer those operations from a successful local request.

Use a secure browser context, keep application secrets outside records and exported files, preserve the output/configuration pair for reproducibility, and distinguish a trusted entropy source from a mathematically reproducible mapping. Local history can be unavailable due to quotas or browser policy; when `storageError` is set, copy/export the current result before leaving.
