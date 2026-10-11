# SDK API · 0.2.0-dev.0

`QVRFClient` and `OnchainQVRFClient` alias `SolanaQVRFClient`.

| Surface | Contract |
| --- | --- |
| constructor | `{ rpcUrl, program, wallet?, quoteUrl?, requestStorage?: 'legacy', symbol? }`; native currency is SOL |
| `quote()` | `{ amount: bigint, recipient, symbol: 'SOL', decimals: 9, capacity: bigint, noRequestRent, ready }` |
| `serviceQuote(requester?, sequence?)` | GET current price or POST bound persisted identity; validates program, recipient, amount, expiry and request identity |
| `approveFee()` | Requires connected wallet and returns quote; no separate allowance transaction |
| `request(input, { expectedFee? })` | Input `{ bytes: 32, mapping, serviceQuote? }`; SDK binds service quote itself; returns schema-4 record after confirmation |
| `getRequest(sequenceOrId)` | Reopens historical account or event record; IDs bind network, program and sequence |
| `waitForResult(id, { timeoutMs?, signal? })` | Polls to fulfilled; default 120000 ms; rejects expiry, cancellation or pending timeout |
| `subscribe(id, listener)` | Polls approximately every 1500 ms; returns unsubscribe function |
| `verify(record)` | Recomputes proof and compares authoritative RPC record fields, mapping and paid receipt |
| `verifyProtocolRecord(record)` | Offline fixed-input digest, commitment chain, domain-separated output and deterministic mapping checks |
| `listRequests()` | Up to 64 newest sequences; availability depends on RPC history |
| `verifyPayment(record)` | Exact transfer, successful transaction, signed sender, memo and same-transaction proof request |
| `expire(id)` | Explicit wallet-authorized terminal expiry; not a refund transfer |

Root also exports `mapOutput`, `ProtocolInput`, `ProtocolRecord`, `RequestRecord` (schema-4 alias), `SolanaWallet`, `MappingConfig`, `Mapping`. `@qentra/sdk/solana` exposes lower-level encoding/payment helpers; `@qentra/sdk/generated` exposes IDL-derived account/event/instruction codecs; `@qentra/sdk/idl` resolves the JSON interface. Generated controller/fulfillment codecs are protocol interfaces, not access to Qentra's private execution service.

`@qentra/sdk/browser` retains the historical browser client, adapter and schema-1 persistence helpers. It is a separate source mode; schema-1 records are not Solana proof records.

Application mapping results are `number | string | string[]`. Quote `amount` and capacity are bigint; use strings for JSON transport. Protocol records already store fees and sequence as decimal strings. No callback registration, escrow allowance, subscription balance or token request API is part of the selected native draw journey. The schema retains callback/delivery fields for compatibility; native draws use zero callback addresses and `not-requested` delivery.
