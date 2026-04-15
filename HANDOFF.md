# Handover — 2026-04-15

**Head commit:** `d5bc9a9` — fix: duplicate URL returns existing peer, AtomicInteger circuit breaker counter
**Previous handover:** `git show HEAD~1:HANDIFF.md`

## What Changed This Session

**Three security/quality fixes shipped:**
- `EncryptionKeyConfigSource` — per-deployment session encryption key (was a shared hardcoded key in the repo). MicroProfile `ConfigSource` at ordinal 200, generates 256-bit key on first boot, persists to `~/.claudony/encryption-key` with `rw-------` permissions. 20 tests.
- Session timeout — `quarkus.webauthn.session-timeout=P7D` (was 30 minutes). Configurable via `claudony.session-timeout`. 3 tests.
- Pre-1.0 ADRs written: `adr/0001` (terminal streaming), `adr/0002` (MCP transport), `adr/0003` (auth mechanism).

**Fleet Manager Phase 1 shipped:**
- Peer mesh: `PeerRegistry` + circuit breaker (3 failures → OPEN, exponential backoff 30s→5m, atomic `peers.json` persistence)
- Three discovery sources: static config (`claudony.peers`), manual (`POST /api/peers`), mDNS scaffold (disabled by default)
- Fleet key auth: `claudony.fleet-key`, `FleetKeyService`, `ApiKeyAuthMechanism` extended to accept peer principal
- Session federation: `GET /api/sessions` fans out to healthy peers (2s timeout), stale cache fallback, `?local=true` prevents recursion
- Full `/api/peers` CRUD: list, add, delete, patch, `/{id}/sessions`, `/{id}/ping`, `/generate-fleet-key`
- Dockerfile (eclipse-temurin:21-jre-alpine + tmux) + docker-compose.yml two-node example
- 45 new tests → total: **207 tests passing**

## Running State

Server and agent are running (JVM mode):
```bash
JAVA_HOME=$(/usr/libexec/java_home -v 26) java -Dclaudony.mode=server -Dclaudony.bind=0.0.0.0 -jar target/quarkus-app/quarkus-run.jar
JAVA_HOME=$(/usr/libexec/java_home -v 26) java -Dclaudony.mode=agent -Dclaudony.port=7778 -jar target/quarkus-app/quarkus-run.jar
```

## Immediate Next Step

**Fleet Phase 2** — dashboard fleet panel, session instance badges, stale session indicators, PROXY WebSocket bridge for peers behind NAT.

No plan file yet. Spec: `docs/superpowers/specs/2026-04-14-fleet-manager-design.md` § "Phase 2 — Fleet UI + terminal proxy".

## Open Questions / Deferred

- **iPad WebAuthn origin** — `192.168.1.108:7777` requires `QUARKUS_WEBAUTHN_ORIGIN` config match. Not yet addressed.
- **Mac Mini deployment + `docs/DEPLOYMENT.md`** — still unwritten; more urgent now Docker exists.
- **mDNS full implementation** — `MdnsDiscovery` is a scaffold; Vert.x mDNS not actually wired.
- **`generate-fleet-key` peer access** — any peer can regenerate the fleet key (future ACL work).

## References

| Context | Where |
|---|---|
| Fleet design spec + FLEET.md | `docs/superpowers/specs/2026-04-14-fleet-manager-design.md`, `docs/FLEET.md` |
| ADRs | `adr/INDEX.md` |
| Living design doc | `docs/DESIGN.md` |
| Latest blog | `docs/blog/2026-04-15-mdp01-one-dashboard-all-sessions.md` |
| Previous handover | `git show HEAD:HANDIFF.md` then `git log --oneline -- HANDIFF.md` |
