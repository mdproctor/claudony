# Handover — 2026-04-29

**Head commit:** `dce90d1` — blog entry + CLAUDE.md update  
**Branch:** `main`, pushed to origin

---

## What happened this session

**Issue #76 — Case worker panel — closed.**

`Session` record expanded from 8 to 10 fields: `Optional<String> caseId` and `roleName` appended after `expiryPolicy`. 20+ construction sites updated across three modules. `withStatus()`/`withLastActive()` propagate both fields. `SessionResponse` exposes them as nullable JSON (NON_NULL — absent for standalone sessions).

`SessionRegistry.findByCaseId(String)` — filters by caseId, sorted by `createdAt` (provisioning order = worker sequence).

`GET /api/sessions?caseId=xxx` — local-only filter, bypasses federation.

`ClaudonyWorkerProvisioner.provision()` stamps `context.caseId()` and `roleName` on the Session.

`session.html` three-panel layout: case workers `<aside>` left, terminal centre, channel panel right. "Workers" toggle button in header.

`terminal.js` case panel: fetch session on init → auto-expand if caseId present → poll `/api/sessions?caseId=` every 3s → render worker rows with status dots → click-to-switch WebSocket + `history.replaceState`. Memory-safe: interval cleared on panel close and `beforeunload`.

**PlaywrightBase fix:** `BASE_URL` was hardcoded `"http://localhost:8081"`. With `quarkus.http.test-port=0`, this caused pre-existing ChannelPanelE2ETest failures (2+6 errors with old code, improved to 1+2 with our fix). Fixed to `ConfigProvider.getConfig().getValue("test.url", String.class)` — Quarkus sets `test.url` after server startup.

**Incidental fix:** `ClaudonyLedgerEventCapture` — `actorType` now resolves from coalesced `entry.actorId` (Refs #53).

---

## Test count

**419 tests** (119 claudony-casehub + 300 claudony-app), 0 failures. Up from 409 — 10 new tests (Tasks 1–6 unit/integration) + 3 new Playwright E2E.

---

## Open epics

**Epic #75 — Three-panel dashboard:**
- #76 ✅ Left panel: case worker panel (closed this session)
- #77 — Right panel: task detail + Qhorus channel (no external blockers)

**Other open:** #93 (concurrent same-role workers — upstream engine change needed)

---

## Immediate next

**#77** — Right panel: CaseHub task detail + Qhorus channel in one side panel. Likely needs a new REST endpoint exposing task/goal data from CaseHub, plus wiring the existing channel panel into the right-panel position when a case worker is selected.

---

## Key files

| Path | What |
|---|---|
| `claudony-core/src/main/java/dev/claudony/server/model/Session.java` | +caseId, +roleName fields |
| `claudony-core/src/main/java/dev/claudony/server/SessionRegistry.java` | +findByCaseId() |
| `claudony-app/src/main/java/dev/claudony/server/model/SessionResponse.java` | +caseId, +roleName nullable |
| `claudony-app/src/main/java/dev/claudony/server/SessionResource.java` | +?caseId= filter |
| `claudony-casehub/src/main/java/dev/claudony/casehub/ClaudonyWorkerProvisioner.java` | stamps caseId + roleName |
| `claudony-app/src/main/resources/META-INF/resources/app/session.html` | three-panel layout |
| `claudony-app/src/main/resources/META-INF/resources/app/terminal.js` | case panel logic |
| `claudony-app/src/test/java/dev/claudony/e2e/PlaywrightBase.java` | BASE_URL via ConfigProvider |
| `claudony-app/src/test/java/dev/claudony/e2e/CaseWorkerPanelE2ETest.java` | 3 Playwright E2E tests |
