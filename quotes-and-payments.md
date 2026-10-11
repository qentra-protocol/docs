# Service quotes and native-SOL payment

Qentra controls the tariff through its service. The initial price is 500000 lamports (0.0005 SOL); read the current quote rather than hardcoding it. Network transaction fees are separate. Default shared-workspace draws create no per-draw rent account. `requestStorage: 'legacy'` is an explicit historical account path and can incur rent.

`quote()` reads the configured service and onchain capacity/shared-workspace state. A mainnet client without `quoteUrl` rejects pricing and requests. Development networks may use the configuration fee when no service endpoint is set. `ready` reflects workspace/pending state; the quote service independently gates pricing on fulfillment health. A ready quote is not a reservation of capacity.

`request()` POSTs `{ program, requester, sequence }` to `quoteUrl`. The response carries `program`, decimal-string `amount`, `recipient`, persisted `id`, `requester`, decimal-string `sequence`, `requestAddress` and Unix-seconds `expiresAt`. It must match the program configuration and derived identity, contain a positive u64 amount, and remain unexpired. The SDK fixes `{ bytes, mapping, serviceQuote: id }` into the request. Encoded fixed inputs must fit 512 bytes.

The externally signed transaction contains: standard System Program transfer to the configured recipient, `QENTRA:request:<requestAddress>` memo, and Qentra proof-request instruction. The service verifies successful confirmation, exact sender signature, recipient/amount and memo, and tracks each payment only once. One wallet call signs this composed transaction. Payment and proof request share atomic inclusion; reveal happens asynchronously afterward.

Use `{ expectedFee: reviewedQuote.amount }` to reject an intervening tariff change before signing. After confirmation, the record's `feeQuoted` is historical evidence and `feePaid` is set only after receipt verification. A future price change never reprices old receipts. `client.verifyPayment(record)` checks against that historical fee, not today's service quote.

Refunds are separately authorized ordinary SOL transfers, tracked in service payment receipts. The thin proof program holds no fee escrow. `expire()` records terminal proof state; it does not move refund funds or guarantee a refund. Preserve the paid request ID/signature when contacting the service about pending fulfillment or its refund receipt.
