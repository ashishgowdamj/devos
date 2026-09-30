# Provenance Record

This file exists so that no downstream reader can mistake the current state of the
specification for a legally clean-room artifact. It is the most important file here.

---

## 1. Reference material and its licence

| Path | Licence | Status |
| --- | --- | --- |
| Reference implementation, application code (its `Sources/`, `CLI/`, native crates) | GPL-3.0-or-later | Quarantined. Copyright its authors and upstream contributors. |
| Reference implementation, server directories (`web/`, `workers/*`, `services/iroh-relay-minter/`, `cmux-tui/relays/cloudflare-do/`) | BUSL-1.1 | Excluded entirely. Not read, not referenced, not ported. |
| Bundled third-party components (terminal emulator, split library, SwiftUI, WebKit) | Their own licences | Must be independently re-vendored from upstream. Never copied from the quarantined tree. |
| Local archive of the original pre-reference DevOS source | GPL-3.0-or-later | Quarantined. Same constraints. |

The quarantined tree is a **GPL work**. A proprietary DevOS cannot be derived from it.
This is a hard constraint, not a preference. Concrete filesystem paths are recorded in the local
provenance log rather than here, so this file can be published.

---

## 2. Who has seen what

| Party | Has read reference source | May write the specification | May implement |
| --- | --- | --- | --- |
| Project assistants | Yes | No — may scope and review only | **No** |
| Owner / product decision-maker | Yes (authored the original source) | Yes — product requirements are the owner's own work | May advise, not author line-by-line |
| **Source-blind observer** (Gate 2) | No — and must remain so | **Yes** | May, if never later exposed |
| **Implementer** (Gate 4) | No — and must remain so | May amend via the protocol | **Yes** |

---

## 3. What the current specification draft is, and is not

A first-pass specification draft exists in a **local, unpublished** working copy. It was compiled
by an assistant that **read the reference implementation's source directly**. It is deliberately
not published in this repository — see [`spec/README.md`](spec/README.md).

It is therefore:

- **Usable for** scoping, effort estimation, architecture brainstorming, building a feature
  backlog, deciding what to cut from the MVP, and reviewing a candidate implementation for
  behavioural gaps.
- **NOT usable as** the legal specification of record. It is source-adjacent. Its factual claims
  about user-visible behaviour are likely correct and independently checkable, but its *provenance*
  is contaminated and it cannot be treated as clean-room output.

### The specific risk

A specification derived from reading an implementation tends to encode that implementation's
*internal* choices as if they were requirements. It launders architecture into contract. Even a
specification that only states observable facts can be shown at trial to have been derived from
the source if the person who wrote it had read the source.

Mitigation: Gate 2 re-derives the specification from the outside, with no access to the
quarantined tree. The draft is used only as a *checklist of hypotheses to verify* — the observer
confirms each claim independently and records the evidence. Claims the observer cannot verify
from outside are either dropped or explicitly marked **unverified**, and unimplemented ones are
not treated as scope.

---

## 4. The practical alternative, stated plainly

There are two coherent routes. The owner should pick one deliberately.

**Route A — Clean-room reimplementation.** What this repository is set up for. DevOS becomes fully
independently ownable and licensable at the cost of reimplementing a large surface area: many
surface types, roughly 150 CLI commands, a socket protocol, agent integrations, a large
configuration schema, a terminal emulator, an embedded browser, and remote infrastructure.

**Route B — Stay GPL.** DevOS remains a GPL derivative. Ownership of the *changes* is possible,
but the product as a whole stays GPL, every downstream user inherits the copyleft obligation, and
it cannot be closed-source or sold as proprietary software. This requires no clean-room work at
all and is materially cheaper.

The current repository assumes Route A. If the owner ever selects Route B, this work is not
wasted — the specification doubles as a feature-parity checklist for a GPL fork — but the
provenance discipline below becomes optional.

---

## 5. Rules that survive either route

1. Nothing in this workspace is copied from the quarantined tree into anything shipped.
2. The BUSL directories are never read, referenced, or used as a specification source.
3. Third-party components are re-vendored from their own upstream projects, with licence
   provenance recorded per component.
4. No upstream hosted service is called by a shipped DevOS build. Hostnames, telemetry
   endpoints, and update feeds must be either owned or user-configurable.
