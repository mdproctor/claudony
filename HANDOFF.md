# Handover — 2026-05-01

**Head commit:** `7428f56` — docs: session handover 2026-04-30
**Branch:** `main`, pushed to origin

---

## What happened this session

**Design analysis session — no code changes.**

**Context rebuild for #77:** explored existing channel panel (fully built — timeline polling, human send, `chLastId` tracking), workers panel, `MeshResource`, `CaseLineageQuery`/`JpaCaseLineageQuery`, and `WorkerSummary` to understand what's already in place.

**Qhorus gateway architectural update:** the Qhorus team added detailed design decisions to casehubio/qhorus#131 since the last handover — Claudony as a Qhorus *participant* (not a layer above), incremental implementation sequence (QhorusChannelBackend → ClaudonyChannelBackend → Slack → WhatsApp), and confirmation that `ClaudonyCaseChannelProvider` is the right starting point for the gateway switch.

**Push model stress-test:** identified 7 maturity concerns with switching from polling to push:

1. Polling catches up automatically; push silently drops on backend outage — needs client-side catch-up (`?after=lastId`) or gateway retry
2. Claudony restart loses backend registrations — `ServerStartup` bootstrap pattern should re-register
3. Fleet gap — only the registering node receives pushed messages
4. Human identity lost through Claudony's proxy — needs structured supplement before gateway hardens
5. `postToChannel` always sends `"status"` — SPI gap in casehub-engine, needs nullable `MessageType` param
6. SSE for case state (replaces 3-second worker poll) — separate improvement
7. Gateway delivery guarantee (Qhorus-internal) — tiered policy per backend

**Issues created:**
- claudony #99 (maturity epic), #100–104
- casehubio/engine #221 (MessageType SPI)
- casehubio/qhorus #132 (delivery guarantee)

All issues include sequencing notes: **none block #77, all are clean retrofits after end-to-end is proven.**

**Blog entry:** `docs/blog/2026-05-01-mdp01-what-polling-was-hiding.md`

---

## Test count

*Unchanged — `git show HEAD~1:HANDOFF.md`*

---

## Open epics

*Unchanged for existing epics — `git show HEAD~1:HANDOFF.md`*

**New:** claudony #99 — channel gateway integration maturity (7 child issues, all deferred post-#77)

---

## Immediate next

**#77** — Right panel: CaseHub task detail + Qhorus channel. No brainstorming session was held this session either — start fresh with brainstorming skill. Context is now solid: the existing channel panel already handles timeline + human send; `JpaCaseLineageQuery` already returns prior workers; no REST endpoint for lineage exists yet. The panel needs a case-context header (role, status, elapsed) + lineage section above the existing channel feed. Build with polling; gateway retrofit comes later.

---

## Key files

*Unchanged — `git show HEAD~1:HANDOFF.md`*
