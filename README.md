# Nebulah Dash

**Development: v0.2.0-alpha.29 · Revision 30 · RecoverySetup**

Open-source Xbox 360 dashboard for RGH/JTAG consoles. Games, homebrew and console tools in one controller-friendly interface. Nebulah Link remains the separate Windows companion.

**Public repository: documentation only. Development status below; no stable release announced.**

## Progress

<!-- nebulah-summary: revision=30; refreshed=2026-10-05; cadence=3 -->

Native dashboard browses scanned titles. Aurora and COD4 launched and remained usable in earlier console tests. Current development adds scan controls, controlled launch-failure recovery and read-only storage review.

## Implemented

- Games/Apps views, paging, title details and results appearing during scans.
- Stop/rescan controls; completed results remain usable after stopping.
- Explicit XEX launch confirmation, fresh header/size checks and failure reports.
- Manual retry for permitted failures; stale evidence requires rescan. Cleanup failure locks all launches until Nebulah restarts.
- Read-only storage review with session-only selection. Existing aliases and volume markers are preserved.

Storage selection does not enroll a drive, change configuration or authorize writes. Scan work runs between frames; individual native I/O calls can still pause the UI.

## Verified

- Current source CI passes.
- Separate database diagnostic XEX build and debug-signature checks pass. This executable contains no dashboard UI.
- Earlier console tests confirm visible UI, controller navigation and default-dashboard return.
- Aurora and COD4 launched and remained usable in targeted console tests. Aurora's missing execution-ID metadata is now a warning.
- Database fresh/reopen and identity-migration tests passed on console, including saved user-state preservation.

Console results apply to their tested builds. **Revision 30 console acceptance remains pending.** Host checks and diagnostic builds do not prove current native UI or game compatibility.

## Pending

- Current-build browse, stop/rescan, retry and storage-review console acceptance.
- Durable storage identity, mount revalidation and actual drive enrollment.
- Normal persistent library writes and saved dashboard settings.
- Physical power-loss durability and broader native crash recovery.
- Covers, network metadata and runtime plugin loading.

**Normal library persistence disabled. Boot replacement disabled. Storage-enrollment writes disabled.**

Recovery handles refusals or returned launch calls while Nebulah still runs. It cannot recover a replacement title after that title takes over.

## Next

Complete current-build console acceptance. Match observations with the build manifest and startup, scan, launch-attempt and storage-review reports. Then resolve mount ownership and revalidation before enabling enrollment.

## Safety

- Launch manually; retain a working dashboard and recovery route.
- Read before writing. Confirm changes; never silently modify NAND or DashLaunch.
- Preserve failed databases, sidecars, markers, favorites and history.
- Never assume USB0 or treat GAME: as durable storage identity.
- No proprietary SDK files, extracted assets, console secrets or game content.

Only reviewed, hardware-tested milestones qualify for public code promotion.
