# Qentra developer guides

Quantum-verifiable randomness infrastructure for onchain applications. Build with randomness ready for the quantum era.

- [Quickstart](quickstart.md): request, wait, map and export.
- [API reference](api.md): typed client and persistence contracts.
- [Helper recipes](helpers.md): tournament winners, allocation applicants and agent candidates.
- [Record format](records.md): inspect and reproduce exported results.
- [Source and trust](source-and-trust.md): entropy origin, mapping verification and delivery boundaries.
- [Development checks](development.md): package assembly and remote-runner expectations.

The [core repository](https://github.com/nicpilsner/project-repo) owns request records, helpers and persistence; the [SDK repository](https://github.com/nicpilsner/qentra-sdk) owns typed request orchestration and the browser adapter. These guides use those implementations rather than embedding another client.
