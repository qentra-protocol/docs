# Native Solana quickstart

Use Node 22+, an explicit RPC endpoint, Qentra's current quote endpoint and a wallet adapter you control. The SDK never creates a wallet or accepts a private key. A wallet implements `{ address: string, signTransaction(wire: Uint8Array): Promise<Uint8Array> }` and returns the signed wire bytes unchanged except for signatures.

Install the reviewed core and SDK tarballs together:

```sh
npm install /path/to/qentra-core-0.1.0-dev.0.tgz /path/to/qentra-sdk-0.2.0-dev.0.tgz
```

```ts
import { QVRFClient, verifyProtocolRecord } from '@qentra/sdk';
import type { SolanaWallet, ProtocolRecord } from '@qentra/sdk';

async function selectWinner(rpcUrl: string, quoteUrl: string,
  wallet: SolanaWallet, save: (r: ProtocolRecord) => Promise<void>) {
  const client = new QVRFClient({ rpcUrl, quoteUrl, wallet,
    program: 'CcoAe9c5yenzozkSqdiJAmaCeoZTiSSV5urBUnC1oa8d' });
  const quote = await client.quote();
  if (!quote.ready) throw Error('Wait for Qentra readiness');
  const pending = await client.request({ bytes: 32,
    mapping: { kind: 'pickUnique', items: ['Amber', 'Birch', 'Cedar'], count: 1 }
  }, { expectedFee: quote.amount });
  await save(pending);
  const result = await client.waitForResult(pending.id);
  await verifyProtocolRecord(result);
  await client.verify(result);
  await save(result);
  return result.mapping!.result;
}
```

Review the quoted amount before calling `request`. That call obtains a requester/sequence-bound persisted quote and composes ordinary SOL transfer, indexed memo and proof-request instructions into one externally signed transaction. Mainnet requires `quoteUrl`. A changed price causes a review error before signing when `expectedFee` is supplied.

Save the ID before waiting. A timeout means reopen this ID, not create/pay another draw. If confirmation itself times out, save the transaction signature reported in the error, inspect its status and recover the associated request through `getRequest`/`listRequests` before retrying. Read [recovery](recovery.md).

For an allocation, fix `mapping: { kind: 'shuffle', items: ['Amber', 'Birch', 'Cedar'] }` before payment. For sampling, use `pickUnique` with a fixed count; for weighted selection use `weightedPick` with fixed weights. Rules, labels and quote identity are included in the input digest; do not remap after the reveal.
