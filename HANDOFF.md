# Handover — 2026-04-15 (session 2)

**Head commit:** `2e082ed` — docs: update test count to 212 after PROXY resize fix
**Previous handover:** `git show HEAD~1:HANDIFF.md`

## What Changed This Session

**Fleet Phase 2 shipped:**
- Dashboard fleet panel: peer health dots, circuit state, source badge, last seen
- Session cards: instance badge, stale indicator (⏰ last seen N ago)
- Add Peer modal (URL, name, terminal mode)
- Ping / remove / toggle terminal mode per peer
- PROXY WebSocket bridge: `ProxyWebSocket` at `/ws/proxy/{peerId}/{sessionId}/{cols}/{rows}`
- Fixed: singleton `HttpClient` in `ProxyWebSocket` (was leaking one per connection)

**Playwright E2E browser tests (13 tests):**
- `PlaywrightSetupE2ETest` — 4 architecture verification tests
- `DashboardE2ETest` — 7 dashboard tests; also fixed `displayName()` bug (remotecc→claudony prefix)
- `TerminalPageE2ETest` — 2 tests (structure + proxy resize URL)
- Convention: `window.__CLAUDONY_TEST_MODE__` gates test hooks; set via `page.addInitScript()`

**PROXY resize fix (#50):**
- New endpoint: `POST /api/peers/{peerId}/sessions/{sessionId}/resize`
- `terminal.js` routes resize to proxy endpoint when `proxyPeer` URL param is set

**Total: 212 Java tests + 13 Playwright browser tests. All issues closed and linked.**

## Running State

*Unchanged — `git show HEAD~1:HANDIFF.md`*

## Immediate Next Step

**Mac Mini deployment + `docs/DEPLOYMENT.md`** — still not written. Now more urgent: fleet, Docker, and Playwright all need to be documented for real-world deployment. Start with `docs/DEPLOYMENT.md` covering JVM jar startup, Docker compose fleet, launchd plist for auto-start, WebAuthn origin config for remote access.

## Open Questions / Deferred

- **iPad WebAuthn origin** — `QUARKUS_WEBAUTHN_ORIGIN=http://192.168.1.108:7777` still not configured
- **mDNS full implementation** — `MdnsDiscovery` is a scaffold; Vert.x mDNS not wired
- **"Expand E2E coverage" epic** — terminal I/O, fleet peer interaction, WebAuthn flows; deferred
- **ClaudeE2ETest cross-test pollution** — leaves tmux sessions that affect `DashboardE2ETest` when full `-Pe2e` runs; tests pass individually
- **`generate-fleet-key` peer access** — any peer can regenerate the fleet key (future ACL work)

## References

| Context | Where |
|---|---|
| Fleet design spec + FLEET.md | `docs/superpowers/specs/2026-04-14-fleet-manager-design.md`, `docs/FLEET.md` |
| Playwright E2E spec | `docs/superpowers/specs/2026-04-15-playwright-e2e-design.md` |
| ADRs | `adr/INDEX.md` |
| Latest blog | `docs/blog/2026-04-15-mdp02-tests-that-make-things-real.md` |
| Previous handover | `git show HEAD~1:HANDIFF.md` then `git log --oneline -- HANDIFF.md` |
