# Export and reopen historical proofs

Protocol records use `schemaVersion: 4`. Save the entire JSON record, including `id`, network/program/sequence, exact `input`, quote identity, digest, contributions, commitment chain depth, output/mapping, original fees, request identity, storage mode and transaction links.

```ts
import { QVRFClient, parseProofFile, exportProofFile } from '@qentra/sdk';
async function reopen(rpcUrl: string, program: string, savedJson: string) {
  const saved = await parseProofFile(savedJson);
  const client = new QVRFClient({ rpcUrl, program });
  await client.verify(saved);
  return client.getRequest(saved.id);
}
```

`id` is `solana:<genesisHash>:<programAddress>:<sequence>`. Cross-network/program IDs reject rather than querying another draw. Reopen is wallet-free. `parseProofFile` validates and verifies before returning. Use `await exportProofFile(result)` to serialize a fulfilled record before sharing. Both preserve schema 4 and exact fixed-input key order.

Default `storage: 'events'` uses a shared reusable workspace. Its indexed request address is read-only and receives no per-draw account allocation. Historical data comes from successful request/reveal/expiration transaction events, not the workspace's current overwritten content. Only events emitted while Qentra is executing are decoded; unrelated nested-program lookalike logs do not establish proof.

`storage: 'account'` remains supported for historical per-request accounts. Transaction links and payment checks supplement decoded account state. `getRequest()` supports both automatically. Event retrieval needs an RPC that retains historical signatures, transactions and logs; recent-history windows are bounded (8 identity signatures, 64 configuration commitment signatures). Missing/pruned history means use an archive-capable RPC or offline saved proof, not fabricate a chain-verification pass.

`verifyProtocolRecord` can reopen exported fulfilled proof data offline after RPC history expires. Preserve evidence before project shutdown. Pending/expired records have no usable checked reveal and cannot pass the fulfilled-result verifier. Schema-1 browser records use the separately retained browser guide/validator, never this Solana codec.

## Receive a tournament or allocation file offline

Install the reviewed core/SDK tarballs together, then run the installed command:

```sh
./node_modules/.bin/qentra-proof verify tournament-proof.json
```

It prints JSON with `scope: "offline-content-integrity"`, the recorded ID, input digest, output bytes and reproduced `mapping` (including `mapping.result`). Invalid files exit 1; invalid arguments exit 2. It reads only the supplied local JSON and never fetches RPC data, discovers a wallet, signs or pays. The same flow works for `pickUnique` tournament winners, shuffled allocations and weighted selections with rules already fixed in `input.mapping`.

```ts
import { readFile, writeFile } from 'node:fs/promises';
import { parseProofFile, exportProofFile, verifyProofFile } from '@qentra/sdk/proof';
// Organizer, after fulfillment:
// await writeFile('draw.json', await exportProofFile(result), 'utf8');
// Recipient:
const saved = await parseProofFile(await readFile('draw.json', 'utf8'));
const proof = await verifyProofFile(saved);
console.log(proof.scope, proof.id, proof.mapping.result);
```

Exports are deterministic for the same record (indented JSON plus newline). Do not sort/rebuild fixed inputs or mapping config: their saved JSON order participates in the existing ABI. Existing fulfilled schema-4 record JSON needs no wrapper. Missing/malformed fields and changed inputs, winners, reveal or output reject before success. Saved verification/payment flags are preserved as metadata, not trusted or promoted by the importer.

Offline recomputation establishes content integrity, not a file's origin, chain inclusion, commitment timing, payment or physical source provenance. Use the explicit RPC comparison above for chain-backed verification. See the [SDK proof-file guide](https://github.com/nicpilsner/qentra-sdk/blob/main/docs/PROOF_FILES.md) and [verification layers](verification.md).
