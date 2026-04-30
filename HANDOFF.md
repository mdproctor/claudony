# Handover — 2026-04-29

**Head commit:** `868972a` — blog entry + CLAUDE.md update  
**Branch:** `main`, pushed to origin

---

## What happened this session

**Brief session — market positioning discussion, then two production bug fixes.**

Positioning: Claudony isn't a terminal emulator. The three-panel dashboard is what differentiates it from CLI wrapper UIs. Even at minimum viable, the codebase is a running reference architecture for CaseHub + Qhorus + Claudony integration.

**Issue #95 — ClaudonyLedgerEventCapture bugs — closed (commit `51db35a`).**

Two production bugs found via external audit (cross-ref casehubio/ledger#72):

**Bug a — silent exception swallowing:** `catch(Exception e)` around `em.persist()`/`em.flush()` was logging and returning normally on any DB failure. Fix: removed the try/catch entirely. `casehub-engine` never had one.

**Bug b — sequence number race:** `MAX(sequenceNumber) + 1` is unsafe under concurrent writes — two threads reading the same MAX before either commits produce silent duplicate sequence numbers. No unique constraint on `(subject_id, sequence_number)` exists — the index name `idx_ledger_entry_subject_seq` misleads. Fix: `ORDER BY sequenceNumber DESC / setMaxResults(1) / findFirst()` — matches casehub-engine's pattern, uses the index.

`ClaudonyLedgerEventCaptureTest` added: 6 tests — happy path fields, sequence increment per case, sequence independence, null guards (2), worker event type.

**CLAUDE.md corrected:** GitHub repo was `mdproctor/claudony` — actual remote is `casehubio/claudony`.

---

## Test count

**425 tests** (119 claudony-casehub + 306 claudony-app), 0 failures.

---

## Open epics

**Epic #75 — Three-panel dashboard:**
- #76 ✅ Left panel: case worker panel (closed last session)
- #77 — Right panel: task detail + Qhorus channel (no external blockers)

**Other open:** #93 (concurrent same-role workers — upstream engine change needed)

---

## Immediate next

**#77** — Right panel: CaseHub task detail + Qhorus channel in one side panel. Needs a new REST endpoint exposing task/goal data from CaseHub, plus wiring the existing channel panel into the right-panel position when a case worker is selected.

---

## Key files

| Path | What |
|---|---|
| `claudony-casehub/src/main/java/dev/claudony/casehub/ClaudonyLedgerEventCapture.java` | try/catch removed; nextSequenceNumber() uses ORDER BY DESC pattern |
| `claudony-app/src/test/java/dev/claudony/casehub/ClaudonyLedgerEventCaptureTest.java` | new — 6 tests for the above |

*Prior session key files — `git show HEAD~3:HANDOFF.md`*
