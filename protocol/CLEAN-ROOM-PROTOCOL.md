# Clean-Room Protocol

Governs every artifact in this workspace. Violating this invalidates the clean-room claim and
reopens Route B in `PROVENANCE.md`.

---

## 1. Roles

| Role | Constraint | Responsibility |
| --- | --- | --- |
| **Owner** | May have full access | Sets product scope, resolves `MIGRATION-DECISIONS.md`, signs off gates. Owns the outcome. |
| **Observer** | Must **never** read the quarantined tree, in whole or in part | Derives and validates the specification from outside evidence. |
| **Implementer** | Must **never** read the quarantined tree, and must not read any source-derived internal design note | Writes DevOS against the frozen specification. |
| **Reviewer** | May read DevOS freely; may read cmux only to test *conformance* | Verifies behaviour matches the specification. |

Two people may hold two roles only if they are the same human, and then only the Observer and
Reviewer roles, which are compatible. **Observer and Implementer are incompatible.** An
Implementer who later needs specification clarification goes through the Owner, never through
the quarantined tree.

---

## 2. Admissible evidence

A specification claim is admissible only if the Observer can cite at least one of:

- **P** — Public documentation: published docs, `--help` output, JSON schema, `man` pages,
  release notes, blog posts, public repositories under a permissive or open licence *other
  than* the quarantined tree.
- **O** — Observed runtime behaviour: running a released binary, exercising the CLI, inspecting
  on-disk state, watching the socket protocol, reading a config file the user already had.
- **I** — Inference from admitted evidence, explicitly labelled as inference with the reasoning
  recorded.

Not admissible: reading the quarantined source, reading a GPL blog post that quotes it, or
relying on the source-adjacent draft in `spec/` as evidence.

Each claim in the validated specification carries a tag: `[P:…]`, `[O:…]`, or `[I: …]`.

---

## 3. Prohibitions

The Observer and Implementer must not:

1. Open, grep, search, or partially read the quarantined tree or its archive.
2. Accept a code snippet, a file path, a type name, or an internal identifier from a
   source-exposed party.
3. Reason from the architecture of the reference implementation. "cmux uses a binary split tree
   with a divider fraction" is architecture. "Panes divide along one of four directions and the
   divider position is stored as a fraction of the pane" is a contract if the fraction is
   observable on the wire or in a data file.
4. Copy strings, comments, documentation prose, or help text verbatim from the reference beyond
   what is required for literal wire or CLI compatibility. Where literal text *is* the contract
   (an error code, a JSON key, a help topic title), it is quoted deliberately and marked.

---

## 4. Gates

### Gate 0 — Quarantine *(complete)*
Licence constraints recorded, reference tree marked read-only-in-spirit, BUSL paths excluded,
provenance register created. Evidence: `PROVENANCE.md`.

### Gate 1 — Discovery *(complete, source-adjacent)*
Broad behavioural inventory compiled to find the true scope of the problem. Not a specification.
Output: the current draft of `spec/BEHAVIORAL-SPEC.md`.

**Exit criteria:** a competent team could estimate the work. Achieved.

### Gate 2 — Source-blind validation
A source-blind Observer independently derives the specification. The Gate 1 draft is used only
as a list of hypotheses. For each claim the Observer records one of:

- `CONFIRMED` with admissible evidence.
- `CORRECTED` with the correct statement and evidence.
- `UNVERIFIABLE` — dropped from scope, or retained in an appendix as an open question.
- `REJECTED` — contradicted by evidence.

Additionally the Observer must record, per section, **what a first-time user could not observe**,
so the Owner can consciously accept or reject each unverifiable requirement.

**Exit criteria:** every normative statement in `spec/` carries a tag; no unverified claim is
load-bearing for the MVP.

### Gate 3 — Freeze
Owner signs off `MIGRATION-DECISIONS.md`: what DevOS copies, what it renames, what it drops.
The specification is frozen as `spec/FROZEN/`. Deviates thereafter require an RFC in this
workspace, reviewed by the Owner, never a silent divergence.

**Exit criteria:** frozen spec exists, decisions recorded, MVP scope enumerated.

### Gate 4 — Implementation
An unexposed Implementer builds DevOS against the frozen spec. The Implementer may ask the Owner
questions; answers that would reveal internals are refused and instead re-derived from public
documentation.

**Exit criteria:** a working build exists.

### Gate 5 — Conformance
A Reviewer runs `spec/ACCEPTANCE-TESTS.md` against the build and records pass/fail. The Reviewer
is permitted to read cmux only to *test* behaviour, never to consult it for design.

**Exit criteria:** all MVP assertions pass; known gaps are documented, not hidden.

---

## 5. Naming policy

Because the goal is an independently owned product, a default is recorded here to prevent the
most likely and most damaging drift.

- **DevOS ships its own names.** The compatibility-preserving names in the specification
  (`cmux` binary, `CMUX_*` variables, `~/.config/cmux/`, `cmux.sock`, the `cmux://` scheme) are
  *legacy compatibility surface*, not DevOS identity. DevOS uses `devos`, `DEVOS_*`,
  `~/.config/devos/`, `devos.sock`, `devos://`.
- **Legacy support is a separable compatibility layer**, so it can be compiled out, and so a
  user migrating from cmux gets a working transition instead of a broken one.
- Every such decision is itemised in `spec/MIGRATION-DECISIONS.md` and needs Owner sign-off at
  Gate 3. The default is set here; the Owner may override it, but must do so explicitly.

---

## 6. Dispute handling

If the Observer and the source-exposed party disagree on a contract, the Observer's
externally-verified reading wins. The source-exposed party may submit additional *evidence* but
may not assert. Every such dispute is logged in `validation/DISPUTES.md` with the resolution and
its basis, so the reasoning is auditable later.
