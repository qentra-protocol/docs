# Pending states without duplicate payment

There are three distinct points:

- Before signing: service quote may reject stale/unhealthy fulfillment, changed price, capacity or occupied workspace. Retry readiness/quote checks; no successful wallet payment has occurred yet.
- After signing/submission: confirmation may be pending or the blockhash may expire. The SDK reports the signature in its error. Save it, inspect the same RPC's transaction/status and recover the indexed request before deciding whether any new transaction is needed. A network error alone is not proof that payment failed.
- After confirmed request: save `record.id` and `requestTx`; polling can time out or the source can remain pending. Call `getRequest(id)`, `waitForResult(id)` or subscribe to this same ID. Do not call `request()` again for a pending paid draw.

`waitForResult` times out after 120 seconds by default. Cancellation stops waiting, not chain processing or payment. An expired record is terminal and must be reconciled with the service's refund receipt; expiry is not an onchain fee refund. Existing paid IDs remain tracked even when new quotes are refused.

```ts
const unsubscribe = client.subscribe(savedId, record => {
  // Save and display the existing record; never pay inside this listener.
  console.log(record.id, record.state, record.paymentVerified);
});
// On leaving the view: unsubscribe();
```

Use caller-owned durable storage. The SDK returns records but does not automatically persist schema-4 data. Catch wait/confirmation errors and retain identity/signature before closing the application. When archived RPC data is missing, keep the saved proof and use an archive-capable endpoint for chain comparison.
