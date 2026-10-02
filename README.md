# Nebulah Dash

**v0.2.0-alpha.11 · Revision 12 · DatabaseVFS**

Open-source Xbox 360 dashboard. Games, homebrew and console tools in one controller-friendly interface.

## Progress

<!-- nebulah-summary: revision=12; refreshed=2026-10-01; cadence=4 -->

Storage discovery and scanner implemented. XEX metadata identifies and groups applications. SQLite foundation separates titles, locations, executables and user preferences.

Current work: prove database persistence on Xbox hardware.

## Built

- Dashboard navigation and placeholder tiles.
- Storage discovery and bounded scanning.
- XEX metadata parsing and application grouping.
- SQLite schema, identity hashes and integrity checks.
- Xbox-native SQLite VFS and database diagnostic.
- Explicit fresh-create and read-only reopen test modes.

## Verified

- Host/source checks passed.
- VFS/repository host tests passed.
- Diagnostic XEX build and signing verification passed.
- Artifact hash and embedded source commit checked.

Evidence: build [957c657](https://github.com/Nebulah360/Nebulah-Dash/commit/957c6574ea14b1c66c287916256ca38540575070), with passing [source checks](https://github.com/Nebulah360/Nebulah-Dash/actions/runs/36943808754) and [XEX build/signing checks](https://github.com/Nebulah360/Nebulah-Dash/actions/runs/36943808768). Development-repository access required.

**Host checks do not prove Xbox compatibility.**

## Pending

- Fresh Xbox DatabaseVFS acceptance.
- Durable storage identity and missing-media handling.
- Full dashboard UI hardware acceptance.

**Normal persistence disabled. Boot replacement disabled. Runtime plugin loading unimplemented.**

## Next

1. Use verified build `957c657` in a fresh test folder. Add only `NebulahDatabase.fresh`, then launch manually.
2. Verify transactions, close/reopen, hashes, integrity, foreign keys and saved scan counts. Confirm dashboard return. Archive the report; retain database and sidecars.
3. Only after fresh PASS, rename the marker to `NebulahDatabase.reopen`. Relaunch the same XEX in the same folder. Verify read-only reopen and archive the second report.

Existing databases block fresh-create. Sidecars block both modes. Never delete evidence to force a pass. The report is replaced each launch.

## Safety

- Launch manually. Keep working dashboard and recovery route.
- Read before writing. Confirm configuration changes. Back up `launch.ini`.
- Never silently modify NAND or DashLaunch.
- Preserve failed databases, sidecars, favorites and history.
- Discover storage roots. Never assume `USB0:` or treat `GAME:` as durable storage identity.
- No proprietary SDK files, extracted assets, system fonts, keys or copyrighted game content. Exclude console secrets and account data from diagnostics.

**Public repository remains documentation-only until stable promotion is approved.**
