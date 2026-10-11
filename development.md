# Package development

This guide covers private-stage tarball consumption, not an npm release. Build the core package from the main repository and install its tarball in the SDK checkout, then run SDK checks and pack. Only execute installs/builds/tests on your isolated development runner.

```sh
# core repository
npm install --no-audit --no-fund
npm run check
npm pack
# SDK repository, with the exact core tarball
npm install --no-audit --no-fund /path/to/qentra-core-0.1.0-dev.0.tgz
npm run check
npm pack
# fresh consumer
npm install /path/to/qentra-core-0.1.0-dev.0.tgz /path/to/qentra-sdk-0.2.0-dev.0.tgz
node /path/to/sdk/examples/packed-consumer.mjs
```

`examples/native-draw.ts` is a callable external-wallet tournament/reopen recipe. It never signs or pays merely by loading. `examples/packed-consumer.mjs` runs a synthetic no-network/no-funds quote → one wallet call → proof → export/reopen fixture using installed package exports. The fixture's wallet/chain responses do not prove live payment, quantum acquisition or validator execution. Native tests cover quote binding/expiry, exact payment identity, generated/IDL byte alignment and pending/cancellation behavior. Historical browser tests/examples remain meaningful under their explicit subpath.

Generated source keeps the canonical IDL and codec behavior; ESM specifiers are made explicit so a packed Node consumer can resolve modules without a source transpiler. Build output has no source maps or absolute source paths. Core peer version and package/API coverage must change together when the shared API changes.
