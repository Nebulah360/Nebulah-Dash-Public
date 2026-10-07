# Nebulah Dash

**Development: v0.2.0-alpha.29 · Revision 30 · RecoverySetup**

Open-source Xbox 360 dashboard for RGH/JTAG consoles. Games, homebrew and console tools in one controller-friendly interface. Nebulah Link remains the separate Windows companion.

**Public repository: documentation only. Development status below; no stable release announced.**

## Progress

<!-- nebulah-summary: revision=30; refreshed=2026-10-05; cadence=4 -->

Native dashboard browses scanned titles. Aurora and COD4 launched and remained usable in earlier console tests. Current development adds scan controls, controlled launch-failure recovery and read-only storage review.

## Built

- Games/Apps views, paging, title details and results appearing during scans.
- Stop/rescan controls; completed results remain usable after stopping.
- Explicit XEX launch confirmation, fresh header/size checks and failure reports.
- Manual retry for permitted failures; stale evidence requires rescan. Cleanup failure locks all launches until Nebulah restarts.
- Read-only storage review with session-only selection. Existing aliases and volume markers are preserved.

Storage selection does not enroll a drive, change configuration or authorize writes. Scan work runs between frames; individual native I/O calls can still pause the UI.

## Verified

- Current [source CI](https://github.com/Nebulah360/Nebulah-Dash/actions/runs/37329494029) passes.
- Separate [database diagnostic XEX build and debug-signature checks](https://github.com/Nebulah360/Nebulah-Dash/actions/runs/37329494173) pass. This executable contains no dashboard UI.
- Earlier console tests confirm visible UI, controller navigation and default-dashboard return.
- Aurora and COD4 launched and remained usable in targeted console tests. Aurora's missing execution-ID metadata is now a warning.
- Database fresh/reopen and identity-migration tests passed on console, including saved user-state preservation.

Both CI runs match source `a56bedc`. Current native dashboard build remains pending.

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

1. Build current private source with `tools/build.ps1 -CheckOnly`, then `tools/build.ps1 -Configuration Release` on the configured Windows RXDK/XDK PC.
2. Copy the new XEX and complete Media folder into a new writable test folder. Retain `UI-BUILD.json` and a working recovery route. Launch manually.
3. Follow the [current console checklist](https://github.com/Nebulah360/Nebulah-Dash/blob/a56bedc1e4d23e61ae9b8e41a0357efa948a6d62/docs/TITLE_BROWSER.md#revision-30-console-test-pending): browse, stop/rescan, disposable-copy refusal/retry, session-only storage review, known-working title launches and dashboard return. No live unplug, power cut or real-marker corruption.
4. Archive startup, stopped/completed scan, each launch-attempt and storage-review reports before replacement. Return them with `UI-BUILD.json` and observations. Development-repository access required.

Then resolve mount ownership and revalidation before enabling enrollment.

## Safety

- Launch manually; retain a working dashboard and recovery route.
- Read before writing. Confirm changes; back up `launch.ini`. Never silently modify NAND or DashLaunch.
- Preserve failed databases, sidecars, markers, favorites and history.
- Never assume USB0 or treat GAME: as durable storage identity.
- No proprietary SDK files, extracted assets, console secrets or game content.

Only reviewed, hardware-tested milestones qualify for public code promotion.
