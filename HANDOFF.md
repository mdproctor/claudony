# Handover — 2026-05-02

**Head commit:** `e380bc6` — chore(casehub): remove buildContext bridge
**Branch:** `main`, CI green (upstream-published chain completed 02:02 UTC)

---

## Workflow convention (new)

**All Claudony work goes on a fork. PRs target casehubio/claudony.** Direct pushes to casehubio/claudony main are no longer the working pattern.

---

## What happened this session

*Previous session content — `git show HEAD~1:HANDOFF.md`*

**CI red → green post-session fix:**

After wrapping, CI was broken because `ClaudonyWorkerContextProvider` implements `buildContext(String, UUID, WorkRequest)` but the published `casehub-engine-api` still had the old 2-arg `buildContext(String, WorkRequest)` interface. Root cause: the engine had lost the 3-arg change — it wasn't in any published PR. The engine team restored it in commit `32b1263` ("feat: expose WorkerContext.channels() via WorkerExecutionContext thread-local"), which also adds `WorkerExecutionContext` — a thread-local holder that gives worker functions access to their case's channels during execution.

Once the engine published, the `upstream-published` chain triggered Claudony CI automatically and it went green at 02:02 UTC. No Claudony code change was needed — `e380bc6` was already correct.

**Engine's `WorkerExecutionContext` (new, from `32b1263`):** thread-local set by `QuartzWorkerExecutionJob` immediately before invoking the worker function. Workers can call `WorkerExecutionContext.current()` inside their function to get their `WorkerContext` including the open case channels. This is the mechanism that will let workers post to channels from inside their function body without needing the channels passed as parameters.

**qhorus#131 context:** the generalised Channel abstraction design — backend-agnostic messaging (Slack, WhatsApp, Matrix, local DB as interchangeable transports), gateway that persists to history store. This is what #98 (ClaudonyChannelBackend) depends on. Currently open/unstarted on the Qhorus side.

---

## Test count

*Unchanged — `git show HEAD~1:HANDOFF.md`*

---

## Open epics

*Unchanged for existing epics — `git show HEAD~1:HANDOFF.md`*

---

## Immediate next

No active feature in flight. Good candidates:
- **#104** — SSE for case state (replaces 3-second worker list poll; self-contained, no dependencies)
- **#98** — `ClaudonyChannelBackend` gateway registration (push to channel panel; depends on casehubio/qhorus#131)
- **#105** — separate MCP endpoints (splits Claudony and Qhorus tools; long-term)

Recommend #104 as the next standalone piece while waiting for qhorus#131.

---

## Key files

*Unchanged — `git show HEAD~1:HANDOFF.md`*
