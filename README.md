# Nebulah Dash

**Development: v0.2.0-alpha.26 · Revision 27 · TitleLauncher**

Open-source Xbox 360 dashboard. Games, homebrew and console tools in one controller-friendly interface. Nebulah Link is the separate Windows companion.

**Public repository is documentation-only. This status describes private development, not a stable release.**

## Progress

<!-- nebulah-summary: revision=27; refreshed=2026-10-05; cadence=4 -->

Native dashboard displays scanned titles with working controller navigation and default-dashboard return. Database diagnostics have passed a console fresh-create/reopen test. Title launching is implemented but remains under investigation.

## Built

- Storage discovery, bounded scanning and XEX application grouping.
- Games/Apps views, paging, title details and rescan.
- Deliberate launch confirmation with executable revalidation.
- SQLite schema, identity hashes, native VFS and diagnostic test modes.
- Startup and launch-stage logging.

## Verified

- Host/source checks and Windows build preflight pass.
- Native UI build, visible video, controls and dashboard return reported working.
- Database fresh/reopen console gate passed: 56 apps, 57 locations, 83 executables.
- DashLaunch launched successfully from the internal HDD in the latest console test.

Host checks do not establish native launch compatibility. Latest UI console reports still need matching build manifests for exact source/artifact provenance.

## Pending

- Games can freeze after launch handoff.
- Aurora is refused by Nebulah's preflight despite launching through XeXMenu.
- Internal-HDD versus USB game comparison is pending; storage is not a confirmed cause.
- Durable storage identity and broader failure recovery remain unfinished.

Preflight refusals and returned launch failures retain the UI. Recovery after another title replaces Nebulah is not implemented.

**Normal library persistence disabled. Boot replacement disabled. Runtime plugin loading unimplemented.**

## Next

Compare the same game on USB and internal HDD using the same build. Capture startup logs and the matching build manifest. Diagnose Aurora's eligibility refusal separately.

## Safety

- Launch manually; retain a working dashboard and recovery route.
- Read before writing. Confirm configuration changes.
- Never silently modify NAND or DashLaunch.
- Preserve failed databases, sidecars, favorites and history.
- Never assume USB0 or treat GAME: as durable storage identity.
- No proprietary SDK files, extracted assets, console secrets or game content.

Only reviewed, hardware-tested milestones qualify for public code promotion.
