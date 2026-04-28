# Handover — 2026-04-28

**Head commit:** `318f64b` — CaseEngine event→ledger→lineage round-trip test  
**Branch:** `main` (uncommitted: CLAUDE.md only — being committed with this handover)

---

## What happened this session

**Epic #86 — Agent mesh infrastructure fully shipped:**

**#87 (CaseChannelLayout SPI):** `CaseChannelLayout` interface + `ChannelSpec`, `NormativeChannelLayout` (work/observe/oversight), `SimpleLayout` (work/observe), `CaseChannelLayout.named()` factory. `ClaudonyCaseChannelProvider` refactored to init-on-first-touch per-case cache. Config: `claudony.casehub.channel-layout=normative|simple`.

**#88 (MeshParticipationStrategy SPI):** `MeshParticipationStrategy` + 3 impls (Active/Reactive/Silent). `ClaudonyWorkerContextProvider` stamps `"meshParticipation"` in every `WorkerContext`. Config: `claudony.casehub.mesh-participation=active|reactive|silent`.

**#89 (System prompt template):** `MeshSystemPromptTemplate` — ACTIVE full template (channels, STARTUP, prior workers, message discipline), REACTIVE reduced (no oversight, no startup registration), SILENT → `Optional.empty()`. Stored in `WorkerContext.properties["systemPrompt"]`. `CaseChannelLayout.named()` extracted to eliminate `selectLayout()` duplication.

**#91 (Playwright channel panel E2E):** `ChannelPanelE2ETest` — 8 tests covering toggle, dropdown, timeline, type badges, human sender, dock post, cursor polling, Ctrl+K.

**#92 (CaseEngine round-trip):** `ClaudonyLedgerEventCapture` — `@ObservesAsync CaseLifecycleEvent` → writes `CaseLedgerEntry`. `JpaCaseLineageQuery` changed to `@Transactional(SUPPORTS)` (safe from IO thread). `CaseEngineRoundTripTest` fires `CaseLifecycleEvent` directly and verifies via `JpaCaseLineageQuery`. `CaseLineageQueryIntegrationTest` (in claudony-app) tests full JPA stack.

**Platform composability diagnosis:** casehub-engine no-op SPI beans are `@ApplicationScoped` (should be `@DefaultBean`) — collide with Claudony implementations when engine is indexed. Vert.x event-bus handlers lack `@Blocking` — JPA from IO thread fails. 6 garden entries submitted.

---

## Test count

**409 tests** (118 claudony-casehub + 291 claudony-app), 0 failures.

---

## Open epics

**Epic #86 — all Claudony-side issues closed.** Upstream items filed:
- casehub-engine no-op beans should be `@DefaultBean` (not `@ApplicationScoped`)
- casehub-engine Vert.x handlers need `@Blocking` for JPA consumers

**Epic #75 — Three-panel dashboard:**
- #76 — Left panel: CaseHub case graph (needs `caseId` on Session model)
- #77 — Right panel: task detail + Qhorus channel
- #91 — ✅ Playwright E2E for channel panel (closed this session)

**Other open:** #93 (concurrent same-role workers — upstream engine change needed)

---

## Immediate next

**#76** — Left panel: add optional `caseId` field to `Session` model + `SessionRegistry`. This unblocks the full three-panel dashboard. No external blockers.

OR: **#84/#85** — CaseHub-level wizard/template generator (future, epic #84).

---

## Key files

| Path | What |
|---|---|
| `claudony-casehub/src/main/java/dev/claudony/casehub/ClaudonyLedgerEventCapture.java` | New: CDI event → ledger writer |
| `claudony-casehub/src/main/java/dev/claudony/casehub/JpaCaseLineageQuery.java` | Changed: `@Transactional(SUPPORTS)` on `findCompletedWorkers()` |
| `claudony-app/src/test/java/dev/claudony/CaseEngineRoundTripTest.java` | New: event→ledger→lineage round-trip |
| `claudony-app/src/test/java/dev/claudony/e2e/ChannelPanelE2ETest.java` | New: 8 Playwright channel panel tests |
| `docs/superpowers/specs/2026-04-27-claudony-agent-mesh-framework.md` | Mesh framework spec — all 3 SPIs documented |
