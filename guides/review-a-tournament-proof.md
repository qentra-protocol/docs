# Review a tournament result before announcing a winner

An organizer sends you a result JSON after a tournament draw. Review both the computed winner and which tournament the file belongs to before announcing the result. You need the original event rules and a trusted request reference from the organizer; the filename alone is not that reference.

## 1. Match the event you intended to review

Keep the event's ordered participant list, selection count and request ID alongside its proof. Compare those original rules with `input.mapping` in the file. A valid proof for another event is not your event's result. Do not reorder participants or replace the mapping with a newly constructed object: the saved input ordering participates in the digest.

## 2. Recompute the file with the installed SDK

With the reviewed SDK and matching core package installed, run:

```sh
./node_modules/.bin/qentra-proof verify tournament-proof.json
```

A successful result contains the ID, input digest, output bytes, recomputed `mapping` and scope `offline-content-integrity`. Compare `mapping.result` with the announced winner. Keep the verifier output and the original file together. A nonzero exit is a failed review, not a reason to edit the JSON until it passes.

## 3. Decide whether you also need a chain read

Offline recomputation checks the file's internal input, reveal, output and mapping consistency. It does not establish who supplied it or whether its transaction was included on the intended chain. For that comparison, construct a read-only `QVRFClient` with the intended RPC and program and use `client.verify(record)` on the parsed record. Keep the original trusted request and transaction references for comparison; do not infer provenance from a saved success flag.

## What to do with each outcome

| Observation | Organizer/recipient action |
| --- | --- |
| File cannot be parsed, or verification rejects | Keep the original file and obtain the correct export; do not announce a winner from it. |
| Verification passes, but participant order, rules or ID differ | Resolve which event the proof belongs to before using the result. |
| Offline review passes, but chain RPC is unavailable | Retain the completed offline review separately and retry the read-only chain comparison when available. |
| Rules, request identity and required verification all match | Record the recomputed winner with the event's proof and review evidence. |

No review step requests a new draw or requires the recipient's wallet. Never pay again to verify a file. See the SDK proof-file reference for supported schema and CLI exit codes.
