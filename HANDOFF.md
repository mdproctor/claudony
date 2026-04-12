# HANDOFF — Claudony

**Date:** 2026-04-13
**Branch:** main (all work merged)

---

## What Happened This Session

Four things, all complete:

1. **Named the project Claudony** — colony metaphor won; bioluminescent visual identity chosen over hex/cyan (too close to QuarkMind)
2. **Landing page live** — Jekyll 4 site at `https://mdproctor.github.io/claudony/`, deployed via GitHub Actions
3. **Internal rename done** — 100% coverage, `dev.remotecc` → `dev.claudony`, all config/strings/docs, 139/139 tests
4. **`target/` added to `.gitignore`** — should have been there from day one

---

## Current State

- App: working, 139 tests pass, runs on Java 26 with `claudony.*` config properties
- Public site: live, auto-deploys from `docs/` on push to main
- Binary name: `claudony-1.0.0-SNAPSHOT-runner`
- Runtime dirs: `~/.claudony/` and `~/claudony-workspace/`
- Auth: passkey re-registration needed on first run (clean cut from `~/.remotecc/`)
- Worktrees: `.worktrees/feat-rename-to-claudony` can be pruned (`git worktree remove`)

---

## What's Next

**Dashboard redesign** — apply bioluminescent colony theme to `/app/`. Landing page visual identity is in `docs/superpowers/specs/2026-04-11-landing-page-design.md`. Design tokens: `--void #05050f`, `--violet #a060ff`, `--green #00ffaa`, `--magenta #cc44ff`. Font: Outfit + JetBrains Mono.

**xterm.js theming** — discussed but not started. Decision: don't force colony palette inside the terminal pane (it's a work surface). Expose configurable presets. Open question: mechanism (URL param, settings UI, or credentials file).

**Session expiry** — session cookies, no max-age. Not implemented.

---

## Key References

- Design doc: `docs/DESIGN.md`
- Design snapshot: `docs/design-snapshots/2026-04-13-post-launch-rename-landing-page.md`
- Landing page spec: `docs/superpowers/specs/2026-04-11-landing-page-design.md`
- App frontend: `src/main/resources/META-INF/resources/app/` (still VS Code dark theme)
- CLAUDE.md: fully updated for Claudony naming
