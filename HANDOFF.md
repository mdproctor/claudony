# Handover — 2026-04-30

**Head commit:** `de790fd` — chore: java-project-health tier-4 fixes  
**Branch:** `main`, pushed to origin

---

## What happened this session

**Maintenance and consistency session — no feature work.**

**Org publishing strategy locked in:** casehub components stay in casehubio org, not submitted to Quarkiverse. Documented in CLAUDE.md and memory so it stops resurfacing.

**Consistency pass** (commits `2562c5d`–`5af398c`, `807c6ee`): DESIGN.md stale package names (`io.quarkiverse.qhorus.*` → `io.casehub.*`), Quarkus version (`3.9.5` → `3.32.2`), Qhorus path, artifact names. CLAUDE.md Stack field corrected. All docs aligned.

**workspace-init skill updated** (pushed to `github.com/mdproctor/cc-praxis`): new Step 1a detects project families — checks for existing `~/claude/private/<parent>/` folder and sibling git repos — and offers nested workspace path (`~/claude/private/casehub/claudony/`) instead of flat.

**java-project-health tier 4** — 17 findings, all fixed (commit `de790fd`):
- `casehub.version` property in root pom; all casehub deps now BOM-managed, no inline versions
- Duplicate `awaitility` removed from claudony-app/pom.xml
- `ObjectMapper` static final field in `SessionResource` (was per-call `new ObjectMapper()`)
- Optional config properties (`default-working-dir`, `credentials-file`, etc.) added as commented examples to `application.properties`
- "Virtual Threads and Blocking I/O" subsection added to DESIGN.md
- Issue-linkage exception policy + commit scope examples added to CLAUDE.md
- `smallrye.config.mapping.validate-unknown=false` in test properties (SRCFG00050 workaround — casehub-qhorus SNAPSHOT bundles `application.properties` with unmapped properties; Quarkus 3.32.2 rejects them at augmentation, surfacing as a misleading classloader failure)
- BUGS-AND-ODDITIES entry #20 added for the above

Garden entry submitted: `GE-20260430-ef928c` — SRCFG00050 classloader symptom.

---

## Test count

*Unchanged — `git show HEAD~5:HANDOFF.md`*

---

## Open epics

*Unchanged — `git show HEAD~5:HANDOFF.md`*

---

## Immediate next

**#77** — Right panel: CaseHub task detail + Qhorus channel. Brainstorming was started (existing channel panel explored, REST endpoints mapped) but paused for this maintenance session. Resume with the brainstorming skill — context needs rebuilding since the session was interrupted.

---

## Key files

*Prior session fixes — `git show HEAD~5:HANDOFF.md`*

| Path | What |
|---|---|
| `pom.xml` | +casehub.version property, all casehub deps in dependencyManagement |
| `claudony-app/pom.xml` | duplicate awaitility removed, casehub deps version-managed |
| `claudony-casehub/pom.xml` | inline dependencyManagement removed, version-managed from root |
| `claudony-app/src/main/resources/application.properties` | commented optional properties |
| `claudony-app/src/test/resources/application.properties` | +smallrye.config.mapping.validate-unknown=false |
| `claudony-app/src/main/java/dev/claudony/server/SessionResource.java` | ObjectMapper static field |
| `docs/DESIGN.md` | Virtual Threads section, test count → CLAUDE.md reference |
| `docs/BUGS-AND-ODDITIES.md` | +entry #20 SRCFG00050 |
