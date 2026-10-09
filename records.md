# Record format and replay

The version 1 record identifies the request and adapter, timestamps input/output activity and keeps a versioned application mapping.

| Field | Meaning |
|---|---|
| `schemaVersion` | `1` |
| `id` | Request identity, up to 100 alphanumeric or hyphen characters |
| `createdAt`, `updatedAt` | Parseable timestamps |
| `adapterId`, `sourceMode` | `qentra-browser-v1`, `browser-cryptographic` |
| `input.bytes` | Requested bytes, integer from 16 to 4096 |
| `inputDigest` | SHA-256 of UTF-8 `JSON.stringify(input)` |
| `outputBytes` | Hexadecimal output; produced records require matching byte length |
| `state` | created, generating, produced or failed |
| `verification`, `delivery` | not-applicable, not-requested for this adapter |
| `events` | Nonempty timestamp/state event list; mapping adds a mapped event |
| `mapping` | Optional version/config/result triple |

```ts
const record = await validateRecord(JSON.parse(exportedJson));
const reproduced = await mapOutput(record.outputBytes!, record.mapping!.config);
console.log(reproduced.result);
```

Use the record validator before replaying externally supplied JSON. The structural schema is in core `schemas/request-record.schema.json`. Runtime validation recomputes the input digest and mapped result. The mapping domain preserves the serialized configuration, lowercase output and counter; configuration property order matters in version 1.

A consistent record explains an application outcome but does not attest entropy origin. Events are operational history, not authenticated proofs. Keep imported files untrusted until the consuming application's own source-authentication policy is satisfied.
