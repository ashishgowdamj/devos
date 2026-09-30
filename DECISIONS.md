# DevOS Migration and Scope Decisions

**Status:** DRAFT — **requires Owner sign-off at Gate 3 before any implementation begins.**

Each decision below has a recommended default derived from `../protocol/CLEAN-ROOM-PROTOCOL.md`
§5. Defaults exist so that implementation does not stall on unasked questions. The Owner may
override any of them, but must do so explicitly.

Override by replacing the `Decision` line and adding a dated rationale.

---

## D-1 — Binary name and `cmux` compatibility

**Question.** Ship `devos` as the only binary, or also install a `cmux` compatibility shim?

**Default.** Ship `devos`. Also install a `cmux` shim on `PATH` that execs `devos`, so existing
scripts, agent instructions, and muscle memory keep working during the transition. The shim is
separable and can be dropped in a later release.

**Impact if declined.** Users migrating from cmux get a hard break. Given the agent hooks and
documented shell idioms in the spec, a silent break would be costly.

**Decision.** *(pending)*

---

## D-2 — Environment variable compatibility

**Question.** Set `DEVOS_*` only, or both `DEVOS_*` and `CMUX_*`?

**Default.** Set both from every terminal; read both, preferring `DEVOS_*`. The five contracted
variables plus the git-watch opt-out are the whole surface, so the cost is small and the
compatibility benefit is high.

**Impact if declined.** Every existing agent hook script, shell integration, and user `export`
breaks on upgrade.

**Decision.** *(pending)*

---

## D-3 — File and directory migration

**Question.** Read legacy `~/.config/cmux/`, `~/.cmuxterm/`, and `~/Library/Application
Support/cmux/`?

**Default.** Yes, read-only, and write all new state to `~/.config/devos/`, `~/.devos/`, and the
DevOS state directory. Offer a one-time explicit import rather than a silent background
migration, and never write back to the legacy paths.

**Impact if declined.** Agent session restore and existing shortcuts break for every current
user. This is the highest-cost decision to decline.

**Decision.** *(pending)*

---

## D-4 — URL schemes, bundle ID, and exported UTIs

**Question.** Register only `devos://` and `com.devos.*`, or keep the cmux registrations?

**Default.** Register both schemes and both UTI sets during the transition. Use
`com.devos.app` as the bundle ID. Dual registration is invisible to users and prevents broken
deep links in bookmarks, CI configs, and notification payloads.

**Impact if declined.** Every existing `cmux://` bookmark, notification, and CI invocation
breaks.

**Decision.** *(pending)*

---

## D-5 — **Scope.** Which subsystems are in the MVP?

This is the decision that determines the size of the project. The full reference surface is
enormous; the default below deliberately cuts it to a coherent, useful product.

### Default MVP (recommended)

| In scope | Why |
| --- | --- |
| Object model: window → workspace → pane → surface, split tree, stable IDs | The foundation everything else needs |
| Terminal surfaces, emulator integration, resize, scrollback, copy-on-select | Core purpose |
| Config file, schema, live reload, settings UI | Configuration must not be an afterthought |
| Shortcut model with chords, `when` clauses, presets | Users coming from cmux expect exact muscle memory |
| Control socket, `system.capabilities`, `system.identify`, events, overload/timeout semantics | Automation is a core differentiator |
| CLI with no-socket help, the nine topics, and the command inventory | Agents depend on offline discoverability |
| Agent session hooks, session store, restore, auto-resume | The agent-integration thesis |
| Feed, permission/plan/question bridge with the 120 s semaphore | The feature users actually notice |
| Agent hibernation with the five gates and confirmation window | Differentiated, and the safety rules are the hard part |
| Notifications, workspace todos, workspace groups | Small, high-value |
| Files, find, markdown, diff viewers | Small surfaces, high utility |
| Custom sidebars (interpreted, hot-reload) | Differentiated, self-contained |
| tmux compatibility layer and `local-tmux` | Distinctive, and a real migration path for tmux users |

### Explicitly deferred (recommended)

| Deferred | Rationale |
| --- | --- |
| Embedded browser and its 40+ command automation surface | Large, and WebKit automation is not a differentiator |
| Canvas free-form layout | Experimental in the reference; large surface for low daily value |
| Cloud machines (36 subcommands) | Requires owned control plane, billing, and images. Not a client-side project. |
| Mobile pairing and the peer-identity transport | Requires owned relays, push infrastructure, and a second app. |
| Presence service | Requires owned backend. |
| Agent session renderer surfaces (React/Solid) | Can be a web surface later |
| Simulator pane | Large, Apple-tooling-dependent, and a niche |
| Computer use / MCP | Security-sensitive; deserves its own design review |
| `ai-accounts`, `glaeda`, coderouter, team features | Depend on a service DevOS does not own |

**The honest cost statement:** the deferred list contains the parts that make this a *platform*
rather than an excellent *terminal multiplexer*. If the goal is a shippable, ownable, genuinely
useful product quickly, the MVP above is the right cut. If the goal is cmux feature parity, the
project is years of work and requires owned backend infrastructure from the start.

**Decision.** *(pending)*

---

## D-6 — Upstream service dependency

**Question.** Should any DevOS build contact upstream cmux infrastructure?

**Default.** **No, without exception.** No `*.cmux.dev`, `*.cmux.iroh`, `files.cmux.com`,
upstream telemetry, upstream update feed, upstream presence, or upstream relay. Sentry DSNs are
opt-in via an env var and empty by default, as they are in the current quarantined build. Any
future hosted service is DevOS-owned or user-configured.

**Impact if declined.** Not applicable. This is a hard constraint from the ownership goal and
from `ACCEPTANCE-TESTS.md` PRV-05.

**Decision.** *(pending — recommend "no, without exception")*

---

## D-7 — Terminal emulator

**Question.** Which terminal engine?

**Default.** Re-vendor Ghostty (or another permissively licensed engine such as libvterm or
SwiftTerm) **from its own upstream**, recording version and licence. Do not copy the reference
tree's fork. The spec's contract is the *behaviour* — live resize, reflow on copy, copy-on-select,
scrollback, non-interactive login shell for initial commands — not any particular renderer.

**Impact.** A renderer choice is a long-term commitment. The spec deliberately keeps terminal
rendering config out of DevOS settings so the engine stays swappable.

**Decision.** *(pending)*

---

## D-8 — Split layout engine

**Question.** Reuse a split library?

**Default.** Either implement the binary split tree directly, or vendor Bonsplit from its own
upstream if its licence permits. The split tree with normalised-fraction dividers and the
declarative layout JSON **are** contracts; the library is not.

**Decision.** *(pending)*

---

## D-9 — Localisation languages

**Question.** Which languages ship at launch?

**Default.** English only at launch, with the string-catalogue structure in place so adding a
language is a catalogue change, not a code change. Japanese is the reference product's second
language and is a reasonable second target.

**Impact if declined.** Shipping two languages doubles the localisation review surface for no
MVP benefit.

**Decision.** *(pending)*

---

## D-10 — Code signing, distribution, and naming

**Question.** What is the shipping identity?

**Default.** Not yet decided, and deliberately so — but two constraints apply now:

1. The **bundle ID, app name, and CLI name are user-visible and effectively permanent** once
   distributed. `com.devos.app` and `DevOS` are recommended, but the availability of
   `devos.app` as a domain and `com.devos.app` as an identifier should be **checked before**
   Gate 3, because a rename later is a migration.
2. Distribution requires a Developer ID and notarisation. That is a prerequisite for Gate 5, not
   a Gate 3 blocker, but it needs lead time.

**Decision.** *(pending)*

---

## Sign-off

Implementation does not begin until every decision above is filled in.

```
D-1  [ ]
D-2  [ ]
D-3  [ ]
D-4  [ ]
D-5  [ ]
D-6  [ ]
D-7  [ ]
D-8  [ ]
D-9  [ ]
D-10 [ ]

Owner: ______________  Date: __________
```

---

## Open questions for the Owner

These are not blocking Gate 2, but they will block Gate 3 or Gate 5.

1. **Is `devos.app` available as a domain, and is `com.devos.app` an unused bundle ID?**
   Determines whether the recommended naming survives contact with the world.
2. **Do you have or plan to own any backend infrastructure** (auth, presence, relays, push,
   cloud machines)? This is the input to D-5's deferred list. Without it, those features stay
   deferred permanently rather than temporarily.
3. **Is a Developer ID available for signing and notarisation?** Gate 5 blocker.
4. **What is the target platform beyond macOS?** The reference has an iOS app; the spec notes it
   but the MVP does not include it. An iOS target changes the architecture before it changes the
   feature list.
5. **Is behavioural compatibility with cmux actually required**, or is only a familiar feel and
   vocabulary needed? If strict wire compatibility is not required, D-1 through D-4 become much
   cheaper and the MVP can be re-cut around DevOS's own design.
