# Nebulah Dash

**Development: v0.2.0-alpha.42 · Revision 43 · LocalSettings**

A controller-driven Xbox 360 dashboard for RGH/JTAG consoles. Browse installed games and apps, view cover art, open details, and launch a selected title.

**Public repository: documentation only. No stable release or public XEX yet.** Development source and console test records live in the private core.

## Progress

<!-- nebulah-summary: revision=43; refreshed=2026-10-09; cadence=4 -->

The dashboard scans local installations, displays cover cards and provides controller-driven Settings. Auto-load covers now saves beside each Dash XEX. The matched console build displayed 58 installations; Off survived relaunch.

Current work: finish preference and missing-cover tests, restore passing source CI, then validate longer-running stability. Normal library persistence and durable storage enrollment remain gated.

## Built

- Library browsing, filters, details, stop/rescan and explicit XEX launch confirmation.
- Fresh header/size checks, controlled retry and refusal reports. Cleanup failure locks launches until restart.
- Local JPEG covers, cover reload and Artwork/Library Settings panels.
- Auto-load covers saved in two checked local slots. This does not enroll storage or enable library writes.
- Read-only storage review with session-only selection.
- [Nebulah Link](https://github.com/Nebulah360/Nebulah-Web-App) remains the separate Windows and phone companion. It can explicitly copy cached covers to a selected Dash folder.

## Verified

- RXDK Release build passed for source `91975931cd7248d074fe878e31f4dda30fb6d830`.
- Matching XEX SHA-256: `08a036fc3e2e4a7621d76a0235b459331621e92088e1f0d008b4bb1cd9d05421`. Console readback matched; structural inspection and launch passed.
- Reference console displayed 58 installations with covers. Auto-load Off survived relaunch. On was saved and covers returned.
- Recorded host syntax, project XML and schema checks passed for the settings work.

Evidence: [current handoff](https://github.com/Nebulah360/Nebulah-Dash/blob/82740feb1b7140b08c3f82e77dd2824fc6076618/docs/HANDOFF.md). Development-repository access required.

**Current [source CI](https://github.com/Nebulah360/Nebulah-Dash/actions/runs/37905954813) fails at “Validate project XML and version metadata.”** Windows UI preflight passes; the full source gate does not.

Structural inspection is not cryptographic signing verification. Independent signing verification of this UI artifact remains unverified. Host checks, builds and targeted console observations do not establish full hardware acceptance.

## Pending

- Auto-load On across another relaunch; acquisition and reload of a newly missing cover.
- Full current-build acceptance, long-duration stability and wider hardware compatibility.
- Durable storage identity, mount revalidation and actual enrollment.
- Normal persistent library writes, physical power-loss durability and broader native crash recovery.
- Direct Dash network downloads and runtime plugin loading.

**Normal library persistence disabled. Boot replacement disabled. Storage-enrollment writes disabled.** Only the local cover preference is saved.

Launch recovery handles refusals or returned calls while Nebulah still runs. It cannot restore Nebulah after a replacement title takes over.

## Next

1. Resolve the source CI failure in private development. Keep each XEX matched to its `UI-BUILD.json` and SHA-256.
2. Use a separate writable test folder with the complete Media assets and a working recovery route. Launch manually.
3. Save Auto-load On, relaunch the same XEX and verify the setting. Test one newly missing cover through explicit Link sync, then Reload local covers.
4. Archive startup, scan, launch-attempt and storage-review reports before replacement. Record outcomes against the manifest; continue stability testing separately.

## Safety

- Read before writing. Confirm changes and back up `launch.ini`. Never silently modify NAND or DashLaunch.
- Preserve failed databases, journals/sidecars, markers, reports, favorites and history. Never delete evidence to force a pass.
- Never assume `USB0:` or treat `GAME:` as durable storage identity. Storage review does not authorize enrollment or library writes.
- No live unplug, power cut or real-marker corruption in these tests.
- No proprietary SDK files, extracted system assets/fonts, keys, console secrets or game content.

Only reviewed, hardware-tested milestones qualify for public code promotion.
