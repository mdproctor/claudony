# Handover — 2026-04-27

**Head commit:** `b24184b` — test: fill coverage gaps
**Branch:** `main` (clean — uncommitted: CLAUDE.md, blog entry, INDEX.md will be committed below)

---

## What happened this session

Large session. Key deliverables:

**CaseHub integration completed (epic #72 — now closed):**
- `JpaCaseLineageQuery` — queries `case_ledger_entry` via `@LedgerPersistenceUnit` EM
- `WorkerSessionMapping` — bridges CaseHub role names to Claudony tmux session UUIDs. Two maps: `caseId:role → sessionId` (precise) and `role → sessionId` (fallback). MVP limitation: concurrent same-role workers across cases → tracked in #93
- `ClaudonyWorkerProvisioner` now returns `Worker(taskType, ...)` — role name from case definition, not UUID — so `WorkResultSubmitter.complete()` can find the worker
- casehub-engine committed `sessionMeta.get("caseId")` fix (commit 535bad6) — now passes caseId to `onWorkerStarted`

**Testing:**
- 334 tests, 0 failures — fixed McpServerIntegrationTest (missing `%test.claudony.server.url=http://localhost:${quarkus.http.port}`), GitStatusTest (wrong org name), TerminalWebSocketTest blank-line (regex break condition, not lastIndexOf)
- `WorkerSessionMappingTest` (10 tests), `MeshResourceInterjectionTest` (+3: EVENT type, ?after cursor, cursor-at-end)

**Mesh framework:**
- Channel panel on session.html — normative type badges (blue=QUERY, teal=RESPONSE, orange=COMMAND, green=DONE, dim=EVENT), human sender highlight, Ctrl+K toggle, interjection dock defaulting to EVENT
- Spec written: `docs/superpowers/specs/2026-04-27-claudony-agent-mesh-framework.md` (718 lines) — 3-channel NormativeChannelLayout, `CaseChannelLayout`+`MeshParticipationStrategy` SPIs, system prompt template, 4-layer normative examples

**Infrastructure:**
- casehub-engine moved: `~/dev/casehub-engine/` → `~/claude/casehub-engine/`
- 4 garden entries submitted (Quarkus test port, WebSocket marker regex, `@TestTransaction`, `quarkus.arc.exclude-types`)

---

## Open epics

**Epic #86 — Agent mesh infrastructure** (all Claudony-side, no blockers):
- #87 — `CaseChannelLayout` SPI + `NormativeChannelLayout` (opens work/observe/oversight)
- #88 — `MeshParticipationStrategy` SPI (ACTIVE/REACTIVE/SILENT)
- #89 — System prompt template in `ClaudonyWorkerContextProvider`
- #92 — Full CaseEngine round-trip E2E (blocked on #87+#88)

**Epic #75 — Three-panel dashboard:**
- #76 — Left panel: CaseHub case graph (needs `caseId` on Session model first)
- #77 — Right panel: task detail + Qhorus channel (Qhorus half buildable now — #76 needed for channel auto-selection)
- #91 — Playwright E2E for channel panel

**Other open:**
- #83 closed; #93 (concurrent same-role workers) — upstream engine change needed
- #84/#85 — CaseHub-level wizard and template generator (future)

---

## Immediate next

**#87** — `CaseChannelLayout` SPI. Pure Claudony work, no blockers:
1. Add interface + `ChannelSpec` record to `claudony-casehub`
2. `NormativeChannelLayout` default implementation (opens work/observe/oversight)
3. Wire into `ClaudonyCaseChannelProvider`
4. Config property `claudony.casehub.channel-layout=normative`

OR pivot to dashboard (#76 — left panel, adding `caseId` to `Session` model).

---

## Key files

| Path | What |
|---|---|
| `docs/superpowers/specs/2026-04-27-claudony-agent-mesh-framework.md` | Mesh framework spec — SPIs, channel layout, system prompt template, 4-layer examples |
| `claudony-casehub/src/main/java/dev/claudony/casehub/WorkerSessionMapping.java` | Role↔session bridge |
| `claudony-casehub/src/main/java/dev/claudony/casehub/JpaCaseLineageQuery.java` | JPA lineage query |
| `claudony-app/src/main/resources/META-INF/resources/app/terminal.js` | Channel panel JS |
| `claudony-app/src/test/resources/application.properties` | `%test.claudony.server.url` fix |
