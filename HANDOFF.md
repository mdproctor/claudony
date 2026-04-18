# Handover — 2026-04-18

**Head commit:** `1b47421` — docs: blog entry Phase 8: The Mesh You Can See
**Previous handover:** `git show HEAD~50:HANDOFF.md` (2026-04-15, session 2)

---

## What happened this session

Enormous session. Three major arcs:

**Arc 1 — Quarkus upgrade + prerequisites (issues #51–53)**
- Quarkus 3.9.5 → 3.32.2 to align with Qhorus
- WebAuthn API replaced entirely: Vert.x WebAuthn → webauthn4j. `CredentialStore` rewritten. `WebAuthnPatcher` + `LenientNoneAttestation` deleted (webauthn4j handles Apple passkeys by default). Graceful migration for legacy non-UUID aaguid strings in `credentials.json`.
- `rest-client-reactive-jackson` → `rest-client-jackson`. `quarkus-junit5` → `quarkus-junit`.
- `McpServer.java` (hand-rolled JSON-RPC) → `quarkus-mcp-server-http`. Tests split: `ClaudonyMcpToolsTest` (direct CDI), `McpProtocolTest` (HTTP compliance), `McpServerIntegrationTest` (real tmux).

**Arc 2 — Reliability + MCP hardening (issues #54–55)**
- Reliability pass: `Await.java` polling utility replacing Thread.sleep across 6 test files; `@TestMethodOrder` removed from 5 test classes; AuthResource stream leak fixed; SessionResource empty catch logged; `catch(Exception)` narrowed to declared types across 5 production files.
- MCP hardening: two-tier error handling (`serverError`/`connectError` helpers) on all 8 `@Tool` methods. `/api/mesh/events` SSE endpoint. Auth protection tests. 4 Playwright E2E tests. XSS-safe `escapeHtml()` in view renderers.
- Key gotcha: `@WrapBusinessError` wraps `IllegalArgumentException` into `ToolCallException` at CDI proxy boundary — must catch both.

**Arc 3 — Qhorus Phase 8 + Mesh panel (issues #56–61, epic #58)**
- `quarkus-qhorus` embedded. 47 tools at `/mcp`. H2 datasource via Hibernate schema generation.
- Ledger write isolation ADR-0004: `@Transactional(REQUIRES_NEW)` + try/catch over async fire-and-forget, to preserve lineage completeness.
- Mesh observation panel: `MeshResource` (thin facade over `QhorusMcpTools`), three views (Overview/Channel/Feed), collapsible, poll/SSE configurable. 9 new Java tests + 4 Playwright E2E.

**Test count: 240** (up from 212)

---

## State

No open GitHub issues. Clean working tree.

---

## What's next

**Immediate:** Human interjection in the Mesh panel — chat input posting to a Qhorus channel as a first-class participant. Design was in progress at session end.

**Near-term:**
- Session expiry enforcement (server-side idle timeout — config exists, enforcement missing)
- Qhorus DB independence refactor (Qhorus session) → then `PeerRegistry` (`peers.json`) migrates to shared DB abstraction

**Medium:** CaseHub embedding (Phase B continued)

---

## Key files

| Path | What it is |
|---|---|
| `docs/superpowers/2026-04-16-mcp-hardening-baseline.md` | MCP hardening history + joint decisions with Qhorus Claude |
| `docs/superpowers/specs/2026-04-17-mesh-observation-panel-design.md` | Mesh panel design spec (approved, implemented) |
| `adr/0004-ledger-write-transaction-isolation.md` | REQUIRES_NEW decision for ledger writes |
| `src/main/java/dev/claudony/server/MeshResource.java` | New: REST facade over QhorusMcpTools |
| `src/main/resources/META-INF/resources/app/dashboard.js` | Updated: MeshPanel, strategies, view renderers, escapeHtml |
