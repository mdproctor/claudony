# Handover — 2026-04-24

**Head commit:** `b5d8e85` — claudony-casehub implementation plan
**Branch:** `main` (plan + spec committed, WIP on worktree)
**Previous handover:** `git show HEAD~1:HANDOFF.md`

---

## What happened this session

Designed and started implementing `claudony-casehub` — the optional Maven module that implements CaseHub Worker Provisioner SPIs in Claudony.

**Completed:**
- Design spec: `docs/superpowers/specs/2026-04-23-claudony-casehub-design.md`
- Implementation plan: `docs/superpowers/plans/2026-04-23-claudony-casehub.md`
- GitHub epic #70, issue #71
- T1 (Maven restructuring) structurally done on branch `feature/claudony-casehub` in worktree `.worktrees/claudony-casehub` — but baseline broken

**Blocker: dependency chain unstable**

Three related issues that must be resolved before T2-T7 can proceed:

1. **`quarkus-ledger` renamed from `1.0.0-SNAPSHOT` → `0.2-SNAPSHOT`** — quarkus-qhorus `runtime/pom.xml` updated to `0.2-SNAPSHOT` but needs verification across all poms. `deployment/pom.xml` had parent version corrupted by sed (fixed). Root: `/Users/mdproctor/claude/quarkus-ledger`

2. **IntelliJ formatter strips `@PersistenceUnit("qhorus")` from quarkus-ledger beans** — Six files affected (`LedgerErasureService`, `LedgerPrivacyProducer`, `LedgerVerificationService`, `LedgerRetentionJob`, `TrustScoreJob`, `JpaActorTrustScoreRepository`). Committed fix at `69a19f5` in quarkus-ledger but IntelliJ reverts working tree. Must `git checkout -- .` THEN build immediately. Garden entry filed: GE-20260424-a29f1c.

3. **`TerminalWebSocketTest` regression in worktree** — `internalBlankLinesPreservedInHistoryToPreservePaneRowPositions` passes on `main` but fails in worktree. Cause: `%test.quarkus.datasource.db-kind=h2` added to `claudony-app/src/main/resources/application.properties` changed Quarkus startup behaviour. Remove it — the real fix is the `@PersistenceUnit` fix in quarkus-ledger.

**WIP commit:** `fb7c555` on `feature/claudony-casehub` in `.worktrees/claudony-casehub`

---

## Immediate next session: fix the dependency chain

**Exact sequence:**

```bash
# 1. Restore quarkus-ledger before IntelliJ touches it
git -C ~/claude/quarkus-ledger checkout -- .
# Verify fix is in place:
grep "PersistenceUnit" ~/claude/quarkus-ledger/runtime/src/main/java/io/quarkiverse/ledger/runtime/privacy/LedgerErasureService.java

# 2. Build quarkus-ledger immediately
cd ~/claude/quarkus-ledger && mvn install -DskipTests -q

# 3. Verify jar has the fix (should show @PersistenceUnit field annotation)
javap -p -classpath ~/.m2/repository/io/quarkiverse/ledger/quarkus-ledger/0.2-SNAPSHOT/quarkus-ledger-0.2-SNAPSHOT.jar \
  io.quarkiverse.ledger.runtime.privacy.LedgerErasureService

# 4. Build quarkus-qhorus
cd ~/claude/quarkus-qhorus && mvn install -DskipTests -q

# 5. Remove workaround from claudony-app application.properties:
# Remove these lines:
# %test.quarkus.datasource.db-kind=h2
# %test.quarkus.datasource.jdbc.url=...
# %test.quarkus.datasource.reactive=false
# %test.quarkus.hibernate-orm.database.generation=none

# 6. Run Claudony tests in worktree
cd ~/.worktrees/claudony-casehub
JAVA_HOME=$(/usr/libexec/java_home -v 26) mvn test
# Target: 275 total, 269 passing, 6 pre-existing failures (no TerminalWebSocket regression)
```

Once baseline is green, continue with T2 (claudony-casehub skeleton).

---

## Key files

| Path | What it is |
|---|---|
| `docs/superpowers/plans/2026-04-23-claudony-casehub.md` | Full 7-task implementation plan |
| `docs/superpowers/specs/2026-04-23-claudony-casehub-design.md` | Design spec |
| `.worktrees/claudony-casehub/` | WIP worktree on `feature/claudony-casehub` |
| `~/claude/quarkus-ledger/` | 0.2-SNAPSHOT, commit 69a19f5 has @PersistenceUnit fix |
| `~/claude/quarkus-qhorus/` | runtime/pom.xml updated to ledger 0.2-SNAPSHOT |
