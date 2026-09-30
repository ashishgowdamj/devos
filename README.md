# DevOS

**An independently owned macOS developer terminal application.**

DevOS is a terminal multiplexer and developer workspace for macOS, built to be fully owned,
independently licensed, and free of any dependency on another product's source code or hosted
infrastructure.

**Status.** Process established. Specification pending source-blind validation. See
[`PROVENANCE.md`](PROVENANCE.md).

---

## Layout

| Path | Purpose |
| --- | --- |
| [`PROVENANCE.md`](PROVENANCE.md) | Provenance record. **Read first.** Records who has seen what, and what that permits. |
| [`DECISIONS.md`](DECISIONS.md) | Scope, naming, and compatibility decisions. Requires owner sign-off. |
| [`protocol/CLEAN-ROOM-PROTOCOL.md`](protocol/CLEAN-ROOM-PROTOCOL.md) | The rules, roles, gates, and prohibitions that govern this project. |
| [`protocol/GATE2-BRIEF.md`](protocol/GATE2-BRIEF.md) | The work order for source-blind specification validation. |
| [`spec/`](spec/) | The behavioural specification. **Empty pending Gate 3** — see [`spec/README.md`](spec/README.md). |
| `validation/` | Source-blind validation records: observations, verdicts, disputes. |
| `implementation/` | The DevOS application. Created only after Gate 3. |

---

## Rules of engagement

1. **The reference implementation is quarantined.** It is a GPL work and cannot be a basis for
   this project. No file is copied from it, and none is read while implementing. Concrete paths
   are recorded in the local provenance log.
2. **No implementation work begins before Gate 3** in
   [`protocol/CLEAN-ROOM-PROTOCOL.md`](protocol/CLEAN-ROOM-PROTOCOL.md).
3. **Anyone who has read the reference source cannot implement DevOS.** They may scope, review
   for conformance, or own the product, but the implementation must be written by someone who
   has not.
4. **The specification describes behaviour, not code.** No pseudocode, no translated source, no
   internal type names, no file layouts from any other implementation.
5. **Every contract claim needs evidence** recorded in `validation/`, traceable to public
   documentation, released binaries, or observed runtime behaviour.
6. **No upstream hosted service is a dependency.** Telemetry, update feeds, presence, and relays
   are either owned or absent. Sentry-style error reporting is opt-in and empty by default.

---

## Current stage

```
[x] Gate 0  Quarantine reference material, record licensing constraints
[x] Gate 1  Behavioural discovery (local, unpublished, scoping only)
[ ] Gate 2  Source-blind observation and specification validation
[ ] Gate 3  Freeze specification, sign off scope decisions
[ ] Gate 4  Implementation
[ ] Gate 5  Conformance verification
```

Proceeding to Gate 2. The discovery draft is held locally and is not published — see
[`PROVENANCE.md`](PROVENANCE.md) §3 for why, and [`spec/README.md`](spec/README.md) for what
replaces it.
