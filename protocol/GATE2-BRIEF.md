# Gate 2 Brief — Source-Blind Specification Validation

**Addressee:** the Observer. You have not read cmux source and must not.

---

## 1. What you are doing

A source-adjacent specification draft exists in `../spec/`. It was compiled by an agent that read
the reference implementation's source. **You must not use it as a specification.** You use it only
as a list of hypotheses to check from the outside.

Your job: independently establish what DevOS's externally observable behaviour must be, using
only admissible evidence, and produce a specification that is defensible as clean-room output.

---

## 2. Hard rules

1. **Do not open the quarantined tree.** Not the reference application checkout, not the local
   archive of the original source, not any blog post that quotes it, not its git history, and not
   its server directories. Concrete paths are in the local provenance log.
2. **Do not ask a source-exposed party for internals.** If you need a fact you cannot observe,
   record it `UNVERIFIABLE`. Do not accept a code snippet, file path, type name, or internal
   identifier from anyone.
3. **Do not read the draft first.** Read §5 of this brief, go do the observation work, and only
   then open the draft to compare. Reading it first will anchor you and destroy the value of this
   exercise.
4. **Record everything.** Every claim ends up in a validation file with evidence. A claim you
   cannot evidence does not enter the specification.

---

## 3. Admissible evidence

- **P — Public documentation.** Published docs, `--help` output from an installed or released
  binary, the published JSON schema, release notes, public blog posts, and public repositories
  under a permissive or open licence **other than** the quarantined tree.
- **O — Observed runtime behaviour.** Running a released binary, exercising the CLI, connecting
  to the socket, reading a config file the user already has, watching files change on disk.
- **I — Inference** from admitted evidence, labelled as inference with the reasoning recorded.

---

## 4. Method

Work outward in. Do not start from the object model, because the draft will feel authoritative.

**Stage 1 — Independent enumeration.** From documentation and a running binary only, write down
what you can observe: the CLI's help topics and command inventory, the config file and its
schema, the environment variables set in a terminal, the on-disk state, the socket protocol, the
event names, the deep-link routes. Record this in `OBSERVATIONS.md` **before** reading the draft.

**Stage 2 — Comparison.** Open the draft. For each of its claims, classify:

| Verdict | Meaning |
| --- | --- |
| `CONFIRMED` | You observed it yourself, with admissible evidence. |
| `CORRECTED` | You observed something different. Record the correct statement and your evidence. |
| `UNVERIFIABLE` | Plausible but not externally observable. Drop it from scope or keep it as an open question. |
| `REJECTED` | Your evidence contradicts it. Record the contradiction. |

**Stage 3 — Unobservable-surface report.** For each section, record **what a first-time user
could not observe**. This is what lets the Owner consciously accept or reject requirements that
are not externally grounded. It is one of the most valuable outputs of this gate.

**Stage 4 — Write the validated specification.** Emit `SPEC-VALIDATED.md` with per-claim evidence
tags, and `DISPUTES.md` for anything where you and a source-exposed party disagreed.

---

## 5. What to prioritise

High value — these are load-bearing for the MVP and cheap to verify:

1. **Help with no app and no socket.** Install or run a released binary and confirm every help
   path works offline. This is a hard contract and easy to check.
2. **The nine help topics**, their exact titles, their aliases, and the unknown-topic fallback.
3. **The command inventory** and each command's help group. Compare against the draft's §2.3 and
   record every command the draft omits as well as every one it invents.
4. **The environment variables** actually set inside a terminal. `env | grep -i cmux` in a real
   terminal is the ground truth.
5. **The config file and schema** — top-level keys, nested keys, `schemaVersion`, and which
   settings the config *refuses* to hold (the terminal-rendering boundary).
6. **The socket protocol** — framing, the request envelope, `system.capabilities`,
   `system.identify`, the `overloaded` and `timeout` error shapes, and the deadlines.
7. **The event catalog** — every event name, the `ack.resume` envelope, the documented bounds
   (4,096 / 16 KiB / 1,024 / 16 MiB), and the `slow_consumer` rule.
8. **On-disk state** — what exists in `~/.cmuxterm/` and `~/.local/state/cmux/` on a machine
   that has actually used the product, and what each file contains.
9. **Deep-link routes** — all six shapes, the strict no-user/no-password/no-port/no-fragment
   rule, and the runtime-then-stable-then-cross-workspace resolution order.
10. **Defaults that are security-relevant** — the socket access modes, the remote relay deny-by-
    default policy, the session-command sanitiser's drop list, the symlink rule for mobile folder
    authorisation, and the transport's "hints are reachability only" rule. If any of these cannot
    be externally verified, say so loudly: they are the contracts whose loss would be a security
    regression, not a feature gap.

Lower value — expensive to verify, and mostly implementation detail:

- The exact factory shortcut table. Verify a sample, not all ~200 rows.
- The 18 agent integration file paths. Verify the ones for agents actually installed on the
  machine.
- Cloud `vm` subcommand flags, if cloud is not in the MVP (see `MIGRATION-DECISIONS.md` D-5).
- Settings section internals and view decomposition.

---

## 6. Deliverables

```
validation/
  OBSERVATIONS.md        Stage 1. What you saw, with evidence, before reading the draft.
  SPEC-VALIDATED.md      Stage 4. The specification of record, with per-claim evidence tags.
  UNVERIFIABLE.md        Everything dropped or held as an open question, and why.
  UNOBSERVABLE.md        Stage 3. Per section, what a first-time user could not observe.
  DISPUTES.md            Stage 4. Disagreements with source-exposed parties and resolutions.
  GATE2-REPORT.md        One page: scope covered, scope not covered, residual risk, verdict.
```

---

## 7. Verdict options

At the end, state one:

- **PASS** — the validated specification is sufficient to implement the MVP against. Freeze it
  and proceed to Gate 3.
- **PASS WITH GAPS** — sufficient, with a named list of gaps the Owner has accepted.
- **FAIL** — the MVP depends on claims that cannot be externally grounded. Re-scope before
  proceeding.

Be willing to return `FAIL`. A specification that launders architecture into contract is worse
than a smaller specification that is honest, because the implementer will build the wrong thing
with confidence.

---

## 8. Order of work

1. Read this brief and `../PROVENANCE.md` only.
2. Enumerate from the outside into `OBSERVATIONS.md`. Do not open `../spec/`.
3. Compare against `../spec/`. Produce verdicts.
4. Write `UNOBSERVABLE.md` — the per-section report.
5. Write `SPEC-VALIDATED.md`, `UNVERIFIABLE.md`, `DISPUTES.md`.
6. Write `GATE2-REPORT.md` and state a verdict.
