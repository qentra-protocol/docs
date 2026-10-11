> Historical browser adapter only (`@qentra/sdk/browser`, schema 1). For native Solana requests use [Quickstart](quickstart.md).

# Development checks

The source packages use Node 22 or newer, TypeScript 5.9.3 and tsx 4.20.6. Core uses idb-keyval 6.2.2. Build core first, create its local package archive, then install that archive to satisfy SDK's exact development peer dependency. This avoids fetching an assumed published Qentra package.

On a no-secrets remote runner, execute the core and SDK `npm run check` commands. They typecheck source/tests/examples, run behavior tests and executable examples, and build ESM declarations. Fetch private repository content only with project-scoped access or upload the approved source courier; never give a package runner a shared account PAT. Record runner resource, revision and outcomes separately for each package.

Core tests cover rejected sampling buckets, helper constraints, replay tampering and memory isolation. SDK tests cover lifecycle sequencing, unsubscribe, failure, cancellation/timeout, storage-failure continuity and adapter limits. IndexedDB requires additional browser-specific runtime coverage beyond the memory-repository checks.

The docs are a source reference. There is no generated documentation-site deployment or package publication in this development layout. Confirm package artifacts and both repository revisions together when integrating the client.
