# Request randomness and map an outcome

Develop from source with Node 22 or newer. Build and pack `@qentra/core` in the core repository, then install that local archive in the SDK repository as described in its README. The development packages have version `0.1.0-dev.0` and are installed from source.

```ts
import { QVRFClient, BrowserEntropyAdapter, MemoryRepository, validateRecord } from '@qentra/sdk';

const client = new QVRFClient({
  adapter: new BrowserEntropyAdapter(),
  repository: new MemoryRepository(),
});
const request = await client.request({ bytes: 32 });
const unsubscribe = client.subscribe(request.id, record => {
  console.log(record.state);
});
try {
  await client.waitForResult(request.id, { timeoutMs: 10000 });
  const mapped = await client.applyMapping(request.id, {
    kind: 'pickUnique', items: ['Amber', 'Birch', 'Cedar'], count: 2,
  });
  const exported = JSON.stringify(mapped);
  const imported = await validateRecord(JSON.parse(exported));
  console.log(imported.mapping?.result);
} finally {
  unsubscribe();
}
```

For browser history replace `MemoryRepository` with `IndexedDBRepository`. Keep a copy/export path visible when `client.storageError` is set. The initial adapter uses browser Web Crypto; inspect [source and trust](source-and-trust.md) before interpreting the record's evidence fields. An executable source-level version is in SDK `examples/request-and-map.ts`.
