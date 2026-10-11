# Export and reopen historical proofs

Protocol records use `schemaVersion: 4`. Save the entire JSON record, including `id`, network/program/sequence, exact `input`, quote identity, digest, contributions, commitment chain depth, output/mapping, original fees, request identity, storage mode and transaction links.

```ts
import { QVRFClient, verifyProtocolRecord } from '@qentra/sdk';
import type { ProtocolRecord } from '@qentra/sdk';
async function reopen(rpcUrl: string, program: string, savedJson: string) {
  const saved: ProtocolRecord = JSON.parse(savedJson);
  await verifyProtocolRecord(saved);
  const client = new QVRFClient({ rpcUrl, program });
  await client.verify(saved);
  return client.getRequest(saved.id);
}
```

`id` is `solana:<genesisHash>:<programAddress>:<sequence>`. Cross-network/program IDs reject rather than querying another draw. Reopen is wallet-free. JSON parse provides transport, not validation; run the verifier before using a saved result.

Default `storage: 'events'` uses a shared reusable workspace. Its indexed request address is read-only and receives no per-draw account allocation. Historical data comes from successful request/reveal/expiration transaction events, not the workspace's current overwritten content. Only events emitted while Qentra is executing are decoded; unrelated nested-program lookalike logs do not establish proof.

`storage: 'account'` remains supported for historical per-request accounts. Transaction links and payment checks supplement decoded account state. `getRequest()` supports both automatically. Event retrieval needs an RPC that retains historical signatures, transactions and logs; recent-history windows are bounded (8 identity signatures, 64 configuration commitment signatures). Missing/pruned history means use an archive-capable RPC or offline saved proof, not fabricate a chain-verification pass.

`verifyProtocolRecord` can reopen exported fulfilled proof data offline after RPC history expires. Preserve evidence before project shutdown. Pending/expired records have no usable checked reveal and cannot pass the fulfilled-result verifier. Schema-1 browser records use the separately retained browser guide/validator, never this Solana codec.
