# API reference

## Client

`QVRFClient` takes an `EntropyAdapter` and `Repository`. `request({ bytes })` accepts 16 to 4096 bytes and returns a created `RequestRecord`. Generation runs asynchronously after the caller can subscribe to the returned ID. `getRequest(id)` queries the session or repository. `subscribe(id, listener)` observes future updates and returns a real unsubscribe function.

`waitForResult(id, { timeoutMs?, signal? })` resolves on produced, rejects on failed, unknown ID, aborted wait or timeout. The default timeout is 10000 ms. Cancellation ends that wait, not the generation request. `applyMapping(id, config)` requires produced output and returns the record with its mapping attached.

`helpers.uniformInt(bytes, min, max)`, `helpers.shuffle(bytes, items)`, `helpers.weightedPick(bytes, items, weights)` and `helpers.pickUnique(bytes, items, count)` return a mapping without attaching it to a request. Use `applyMapping` to persist one on a request.

## Storage and imports

`Repository` exposes `put(record)`, `get(id)` and `list()`, all asynchronous. `MemoryRepository` clones stored/retrieved objects; `IndexedDBRepository` validates browser records through idb-keyval 6.2.2. `storageError` reports failed persistence while keeping the session result usable.

`validateRecord(unknown)` checks supported browser source/status fields, input digest, output byte length and mapping reproduction, then returns a clone. `mapOutput(bytes, config)` reproduces a mapping from exported hexadecimal bytes. These functions and types are re-exported from the SDK's core peer dependency.
