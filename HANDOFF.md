# Handover — 2026-04-24

**Head commit:** `bc0fda3` — blog: four-spis-two-traps
**Branch:** `main` (clean, nothing in progress)
**Previous handover:** `git show HEAD~1:HANDOFF.md` (now obsolete — epic closed)

---

## What happened this session

Completed and merged epic #70 / issue #71: the `claudony-casehub` module.

**Dependency chain fixed first:**
- `quarkus.ledger.datasource=qhorus` routes ledger's EntityManager to the
  named Qhorus PU (undocumented property, only in `LedgerConfig.java` source)
- Removed bad `%test.quarkus.datasource.*` workaround from `application.properties`

**Four SPI implementations in `claudony-casehub/`:**
- `ClaudonyWorkerProvisioner` — tmux sessions prefixed `claudony-worker-{uuid}`
- `ClaudonyCaseChannelProvider` — Qhorus channels `case-{caseId}/{purpose}`
- `ClaudonyWorkerContextProvider` — uses `CaseLineageQuery` interface (not
  `CaseLedgerEntryRepository` directly — that class has no `@ApplicationScoped`)
- `ClaudonyWorkerStatusListener` — ACTIVE/IDLE/FAULTED lifecycle + stall CDI event

**Two gotchas found and garden-submitted:**
- `@ConfigMapping` in library JAR causes SRCFG00050 when properties exist in
  `application.properties` — timing issue; fix is to not ship the properties
- `CaseLedgerEntryRepository` is not a CDI bean — fixed via `CaseLineageQuery`
  interface + `@DefaultBean EmptyCaseLineageQuery`

**State:** Feature branch `feature/claudony-casehub` merged, deleted, worktree removed.
307 tests total (32 casehub + 275 app), 4 failures + 2 errors all pre-existing.

---

## Immediate next: nothing blocked

Main is clean. No WIP. The natural next areas:

1. **Wire `CaseLineageQuery` for real** — add `quarkus.datasource.casehub.*` named
   datasource and a JPA-backed `CaseLineageQuery` implementation. The scaffold is
   there (`@Alternative`/`@Priority` replaces `EmptyCaseLineageQuery`).

2. **End-to-end CaseHub integration** — exercise `claudony.casehub.enabled=true`
   with a real CaseHub engine. The SPIs are implemented but never called from
   a live CaseEngine.

3. **Three-panel unified dashboard** — the design spec has the layout;
   nothing implemented yet. See `docs/superpowers/specs/2026-04-13-quarkus-ai-ecosystem-design.md`.

---

## Key files

| Path | What it is |
|---|---|
| `claudony-casehub/src/main/java/dev/claudony/casehub/` | All four SPI implementations |
| `claudony-casehub/src/main/java/dev/claudony/casehub/CaseLineageQuery.java` | Interface to swap for JPA impl |
| `docs/superpowers/specs/2026-04-23-claudony-casehub-design.md` | Design spec |
| `docs/superpowers/plans/2026-04-23-claudony-casehub.md` | Plan (now complete) |
