# Specification

**This directory is intentionally empty in the public repository.**

The DevOS behavioural specification is not published here yet, by design.

## Why

DevOS is being built as an independent implementation. To keep it genuinely independently
ownable, the specification must be derived from **externally observable behaviour only** — public
documentation, released binaries, and observed runtime behaviour — by an author who has never
read any source of the reference implementation.

A first-pass draft exists, but it was compiled by an author who had read the reference
implementation's source. That makes it a useful scoping and planning document, and an
**unusable** clean-room specification: a document derived from source can encode that source's
internal design as if it were a requirement, and its provenance cannot be cured after the fact.

Publishing it would create a permanent, public record of derivation, which is precisely what this
project exists to avoid.

## When it appears here

The specification is published at **Gate 3**, when:

1. A source-blind observer has independently re-derived it (`../protocol/GATE2-BRIEF.md`).
2. Every normative claim carries admissible evidence.
3. Claims that could not be externally verified are dropped or listed as open questions.
4. Scope decisions are signed off (`../README.md` tracks gate progress).

At that point this directory will hold:

| File | Contents |
| --- | --- |
| `BEHAVIORAL-SPEC.md` | Object model, surfaces, agents, UI. Evidence-tagged. |
| `INTERFACE-MATRIX.md` | Every externally visible interface: CLI, socket, config, environment, URLs, files. |
| `ACCEPTANCE-TESTS.md` | Externally observable conformance assertions. |
| `MIGRATION-DECISIONS.md` | Signed-off naming, compatibility, and scope decisions. |
| `FROZEN/` | The frozen specification implementation is built against. |

## In the meantime

Scope decisions are tracked publicly in [`../DECISIONS.md`](../DECISIONS.md), because deciding what
to build does not require reading anyone's source. The process is in
[`../protocol/CLEAN-ROOM-PROTOCOL.md`](../protocol/CLEAN-ROOM-PROTOCOL.md).
