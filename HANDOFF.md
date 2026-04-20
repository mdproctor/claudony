# Handover — 2026-04-20

**Head commit:** `26a772d` — docs: human interjection — update test count to 246
**Previous handover:** `git show HEAD~1:HANDOFF.md` (2026-04-18)

---

## What happened this session

Human interjection in the Mesh panel — epic #62, issues #63–65, all closed.

**Feature:** Fixed dock pinned below `mesh-body`, visible in all three views. Channel `<select>` defaults to most-recently-active (sorted by `lastActivityAt`), updates on channel-item click in Overview/Feed/Channel. Type select (status/request/response/handoff/done). Enter to send, inline 4-second error, immediate poll trigger on success. Double-submit guard.

**Backend:** `POST /api/mesh/channels/{name}/messages` in `MeshResource`. `VALID_HUMAN_TYPES` guard → 400. `ToolCallException.getCause() instanceof IllegalArgumentException` → 404; other `ToolCallException`/`IllegalStateException` → 409. Sender fixed as `"human"`.

**Key bugs found during implementation:**
- `var(--bg-secondary)` undefined in CSS → transparent inputs. Fixed to `var(--bg)`.
- `escapeHtml()` in `onclick` doesn't prevent JS injection — `;` and `)` pass through. Fixed with `data-channel` attribute + `querySelectorAll` binding after `innerHTML`.
- `%test.quarkus.datasource.reactive=false` required when `hibernate-reactive-panache` is transitive dep with H2 — whole test context fails to start without it.
- `@WrapBusinessError` wraps into `ToolCallException(cause)` — `getCause()` instanceof check needed to distinguish 404 vs 409.

**Tests:** 246 passing (was 240). `MeshResourceInterjectionTest` (4), `MeshResourceAuthTest` +1, `StaticFilesTest` +1. E2E: `MeshInterjectionE2ETest` (3, `-Pe2e`).

**CLAUDE.md:** Two new test conventions documented — `UserTransaction` cleanup pattern for HTTP-triggered DB state, `%test.quarkus.datasource.reactive=false` requirement.

---

## State

No open GitHub issues. Clean working tree after commit.
`application.properties` now has `%test.quarkus.datasource.reactive=false`.

---

## What's next

**Immediate:** Session expiry enforcement — server-side idle timeout. Config exists (`claudony.session-timeout`), enforcement not implemented. Sessions never expire.

**Near-term:** Qhorus DB independence refactor (Qhorus session) → then `PeerRegistry` (`peers.json`) migrates to shared DB abstraction.

**Medium:** CaseHub embedding (Phase B continued).

---

## Key files

| Path | What it is |
|---|---|
| `docs/superpowers/specs/2026-04-18-human-interjection-design.md` | Interjection spec (marked Implemented) |
| `src/main/java/dev/claudony/server/MeshResource.java` | POST endpoint + error handling |
| `src/main/resources/META-INF/resources/app/dashboard.js` | Dock wiring — MeshPanel._initDock, _send, _updateDockChannels, selectChannel |
| `src/test/java/dev/claudony/server/MeshResourceInterjectionTest.java` | Integration tests with UserTransaction cleanup |
