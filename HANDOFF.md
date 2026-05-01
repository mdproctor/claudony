# Handover — 2026-05-01

**Head commit:** `673a4c5` — docs: add blog entry 2026-05-01-mdp02
**Branch:** `main`, pushed to origin

---

## What happened this session

**#77 shipped end-to-end.** Case context panel: role/status/elapsed header, collapsible lineage (polling `GET /api/sessions/{id}/lineage` every 60s), channel auto-select to `case-{caseId}/work`. All 119 casehub + 310 app tests green. 28 E2E tests pass.

**Three upstream API migrations bundled:**
- `WorkerContext.channel` → `channels (List<CaseChannel>)` — casehub-engine commit 73fda63
- `WorkerContextProvider.buildContext` 2-arg → 3-arg `(workerId, caseId, task)` — engine PR #224
- Qhorus `sendMessage`/`createChannel` — non-@Tool overloads made package-private; all call sites updated to 9-arg public signatures

**MCP tool pagination fix (#105 filed):** quarkus-mcp-server defaults to `tools/list` page-size=50, silently dropping tools alphabetically beyond position 50. Fixed with `quarkus.mcp.server.tools.page-size=0`. Long-term: separate endpoints (issue #105).

**Messaging conventions fixed (#106):**
- EVENT feed renderer now shows `tool_name`/`duration_ms`/`token_count` (EVENT content is null by design)
- `NormativeChannelLayout.allowedTypes` now passed to Qhorus on channel creation — oversight channel enforces QUERY+COMMAND at infrastructure level
- Interjection dock default: `event` → `command`
- Dock type options filtered dynamically to channel's permitted types on channel select

**Playwright robustness fixes:**
- `<option>` in `<select>` → use `WaitForSelectorState.ATTACHED` not default visible
- `standaloneSession_noCaseHeader` uses `waitForLoadState(NETWORKIDLE)` instead of fixed timeout
- `interjectionDock` explicitly selects `status` type (EVENT content is null now)

**Garden sweep:** 5 entries submitted (quarkus-mcp-server 50-tool cap, Playwright option visibility, Qhorus testing module staleness, javap+jandex diagnosis technique, quarkus.mcp.server.tools.page-size undocumented).

---

## Test count

**428 tests** (119 casehub + 310 app) + 28 E2E (separate -Pe2e run). Previous: 425.

---

## Open epics

*Unchanged for existing epics — `git show HEAD~1:HANDOFF.md`*

**Closed:** #77 — case context panel shipped.

**New:** #106 — messaging conventions (closed same session). #105 — separate MCP endpoints (open, long-term).

---

## Immediate next

No active feature in flight. Good candidates:
- **#98** — `ClaudonyChannelBackend` gateway registration (replaces channel panel polling with push; depends on casehubio/qhorus#131)
- **#104** — SSE for case state (replaces 3-second worker list poll)
- **#105** — separate MCP endpoints (splits Claudony and Qhorus tools; prerequisite: Qhorus standalone MCP)

Recommend: let the other repos (qhorus#131) settle first, then pick up #98 as the next significant piece.

---

## Key files

*Unchanged — `git show HEAD~1:HANDOFF.md`*
