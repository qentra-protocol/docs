# Qentra Protocol developer guide

Randomness infrastructure for the quantum era. Build tournament winners, allocation draws and agent selection with checkable quantum-origin draws and an externally signed native-SOL request.

Start with [Quickstart](quickstart.md), then inspect [API](api.md), [quotes and payment](quotes-and-payments.md), [proof verification](verification.md), [export and reopen](records.md) and [pending recovery](recovery.md). [Helper reference](helpers.md) describes deterministic application mapping. [Architecture and trust](source-and-trust.md) identifies the checks and service boundaries.

The Solana program is `CcoAe9c5yenzozkSqdiJAmaCeoZTiSSV5urBUnC1oa8d`. [Inspect on mainnet](https://explorer.solana.com/address/CcoAe9c5yenzozkSqdiJAmaCeoZTiSSV5urBUnC1oa8d). Qentra's site is [qentra.org](https://qentra.org).

Repository family: [core and helpers](https://github.com/nicpilsner/project-repo), [SDK and generated interface](https://github.com/nicpilsner/qentra-sdk), this developer guide. Package version covered: `@qentra/sdk@0.2.0-dev.0`, core peer `0.1.0-dev.0`, protocol record schema 4. Use reviewed local tarballs while package publication is pending; see [development](development.md).

Historical browser adapters remain available under `@qentra/sdk/browser`; their schema-1 guide is retained in `legacy-browser-*.md`. The primary `QVRFClient` is the Solana client.

Proof recipients can run `./node_modules/.bin/qentra-proof verify draw.json` after installing the reviewed core/SDK packages. The command reproduces the winner/allocation offline, without wallet, payment or RPC. Follow [proof export and import](records.md) and [verification layers](verification.md).
