# Handover — 2026-04-23

**Head commit:** `ea31bb3` — blog: everything-in-its-own-datasource
**Previous handover:** `git show HEAD~1:HANDOFF.md`

---

## What happened this session

**Ecosystem persistence isolation** — design, implementation across two projects, ADRs, blog.

**Qhorus changes (on `main`, pushed):**
- `PendingReplyStore` as the sixth store SPI interface (+ reactive mirror, JPA + InMemory impls, contract test base, `deleteExpiredBefore` returns `long`)
- `MessageStore` aggregate methods: `countAllByChannel()`, `distinctSendersByChannel(UUID, MessageType)` — closed all `Message.getEntityManager()` bypasses in `QhorusMcpTools` and `ReactiveQhorusMcpTools`
- Named persistence unit `qhorus` — `AgentMessageLedgerEntryRepository` uses `@PersistenceUnit("qhorus")`; test `application.properties` updated to `quarkus.datasource.qhorus.*`
- InMemory*Store timestamp init fix + `getChannelTimeline` bypass fixed
- ADR 0001: Claudony is not a dependency of CaseHub
- Qhorus #87 open: `ReactiveJpaMessageStore.countAllByChannel` loads all rows (FIXME comment in place)

**Claudony changes (on `main`, pushed):**
- `quarkus.datasource.qhorus.*` named datasource (was default datasource)
- `quarkus-qhorus-testing` test dependency — Qhorus data now uses InMemory stores in `@QuarkusTest`, no real DB needed
- `MeshResourceInterjectionTest` cleanup: inject `InMemoryChannelStore` + `InMemoryMessageStore`, call `clear()` in `@AfterEach`
- `MeshResource` fixes: `QhorusMcpToolsBase` inner class refs, SSE worker thread dispatch
- `src/test/resources/application.properties` with `quarkus.http.test-port=0`
- ADR 0005: CaseHub integration lives in optional `claudony-casehub` module
- Ecosystem design updated: `NonoProvisioner` added
- 275 tests passing (6 pre-existing connection-refused failures need live server)

---

## State

Clean on `main`. Both Qhorus and Claudony pushed to GitHub.
`settings.local.json` modified (unrelated).

---

## What's next

**Immediate:** Qhorus #87 — fix `ReactiveJpaMessageStore.countAllByChannel` to use GROUP BY aggregate query instead of loading all rows.

**After that:** CaseHub Phase B embedding.
- Survey `~/claude/casehub` to see how far along the SPIs are
- `claudony-casehub` module in Claudony implementing `WorkerProvisioner`, `CaseChannelProvider`, `WorkerContextProvider`, `WorkerStatusListener`
- `quarkus.datasource.casehub.*` named datasource (convention already in spec)
- Unified three-panel dashboard

**Standing idea:** Get Qhorus bootstrapped under Claudony management — every cross-project question is currently a round-trip message.

---

## Key files

| Path | What it is |
|---|---|
| `docs/superpowers/specs/2026-04-22-ecosystem-persistence-isolation-design.md` | Spec for this session's work |
| `docs/superpowers/specs/2026-04-13-quarkus-ai-ecosystem-design.md` | Master ecosystem design (CaseHub Phase B blueprint) |
| `adr/0005-casehub-integration-is-optional.md` | Claudony dependency boundary rule |
| `~/claude/casehub/adr/0001-claudony-is-not-a-dependency.md` | CaseHub dependency boundary rule |
| `~/claude/quarkus-qhorus/` | Qhorus repo — all changes on main |
| `~/claude/casehub/` | CaseHub repo — next implementation target |
