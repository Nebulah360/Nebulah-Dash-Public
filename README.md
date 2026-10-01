# Nebulah Dash

**Nebulah Dash** is a new Xbox 360 homebrew dashboard project intended to become a modern replacement shell for modified Xbox 360 consoles while preserving the workflows the community relies on.

> Current version: **0.2.0-alpha.11**
> Project revision: **12**
> Codename: **DatabaseVFS**  
> Status: **Revision 12 host/source checks and diagnostic XEX signing verification passed; fresh Xbox DatabaseVFS acceptance pending; normal persistence disabled**

## Project goals

Nebulah Dash is being designed to eventually provide:

- Full dashboard replacement behavior
- Xbox 360, XBLA, original Xbox, emulator, and homebrew libraries
- Title scanning, metadata, favorites, and recent games
- DashLaunch configuration editing
- FTP and remote file transfer
- Console, storage, network, and display settings
- Plugin loading and plugin management
- Community service adapters such as SNet and LiNK/XboxUnity
- A curated homebrew/package browser informed by GitHub release and star data
- Remote management through a versioned API
- Companion applications for Windows, Linux, macOS, iOS, and Android

The project is starting with the console dashboard first. Network services, package installation, configuration writes, and plugin loading will be added only after the base render/input/launch architecture is stable.

## What revision 12 contains

Revision 12 builds on the accepted Revision 9 scanner pipeline and the Revision 10/11 database work. It includes storage discovery, bounded application/XEX enumeration, metadata parsing, identity fusion and application aggregation; SQLite schema v1 and deterministic identity/record hash contracts; and the Xbox-native SQLite VFS and repository diagnostic. Revision 12 fixes native file ownership, EOF handling and error propagation, with production VFS/repository integration checks through a host file shim.

The database diagnostic writes scanner records transactionally to a dedicated test database, closes and reopens it, verifies schema and record hashes, and checks integrity and foreign keys. Host/source checks and the signed diagnostic build passed; fresh Xbox DatabaseVFS acceptance is still pending. Normal persistent scanner commits remain disabled.

The current 360Dash source includes:

- Native Xbox 360 executable project
- RXDK-360 modern Visual Studio toolchain configuration
- D3D9 device initialization at a 1280x720 logical dashboard surface
- XUI initialization
- Use of the Xbox 360 system font from flash rather than redistributing a Microsoft font
- Controller input with edge detection
- HOME / SOCIAL / GAMES / APPS / SETTINGS navigation
- Six focusable dashboard tiles per tab
- Metro-inspired tile layout
- Build/version information in the UI
- Clean separation between application state, platform code, and UI rendering

The buttons currently exercise navigation only. They intentionally do not modify console configuration in this revision.

## Revision 12 VFS validation

Revision 12 keeps normal library persistence disabled. It enforces one native
owner per database file, handles native EOF as a zero-filled SQLite short read,
and propagates access/delete/close failures. A host file shim exercises the real
Xbox VFS and repository with pinned SQLite: schema and scanner-record persistence,
close/reopen, hashes, integrity, rollback, user-state preservation and corruption.
These host checks do not establish Xbox ABI/FATX correctness; a new console report
is required. Preserve the failed revision-10 DB and use a separate empty testing
folder for the fresh-create run. See `docs/HANDOFF.md` and `AGENTS.md`.

## Revision 11 DatabaseVFS

Revision 11 is the first hardware-fix pass for the Xbox-native SQLite layer. The initial Trinity database run reached SQLite but failed at `PRAGMA user_version` with `file is not a database`. SQLite is now explicitly compiled with `SQLITE_BYTEORDER=4321` for the Xbox 360's big-endian PowerPC CPU instead of relying on cross-compiler autodetection.

The diagnostic also records immediate open/schema status and raw database header probes so future VFS failures can be separated into file-I/O, SQLite-format, schema, record-hash, and integrity stages.

## Revision 10 SQLite hardware database test

Revision 10 uses the pinned official SQLite 3.53.4 amalgamation with Nebulah's own Xbox 360 VFS. The diagnostic creates `GAME:\NebulahLibraryTest.db`, writes the current scanner records transactionally, closes it, reopens it, verifies the compiled migration SHA-256 against the stored schema migration, recomputes application/location hashes and executable IDs, and runs SQLite `integrity_check` and `foreign_key_check`.

This database is deliberately a hardware-validation database. Normal persistent library mode remains disabled until the VFS and verification pass on console.

## Milestone 5 library database foundation

The persistent library foundation is now under construction. Schema v1 separates
logical applications, physical locations, individual XEX files, scan history,
storage identity, and user state.

Nebulah now includes a portable SHA-256 core and deterministic application,
location, executable, application-record, and location-record hash contracts.
The exact schema migration is also hashed and checked against a compiled
baseline before it becomes eligible for on-console persistence.

The Revision 9 Trinity acceptance inventory is preserved as a sanitized,
hash-verified regression fixture so later database changes can be checked
against known real-hardware scanner behavior.

See [Library Database Foundation](docs/LIBRARY_DATABASE.md).

## Revision 9 scanner inventory

Revision 9 turns the per-XEX scanner output into temporary application-level records. Games with alternate modes remain one game, dashboard shell modules remain one dashboard, DLL-style components remain components, trainers/loaders remain helpers, and invalid extension-only XEX hits remain non-executable.

The diagnostic emits `appaggregate=` records and a `[ScannerInventorySummary]` section intended as the overall milestone-4 game/homebrew scanner acceptance test.

See [Application Aggregation and XEX Family Fingerprints](docs/APPLICATION_AGGREGATION.md).

## Revision 8 identity fusion

Revision 8 replaces the diagnostic's previous max-score identity hint with the reusable `XexIdentityFusion` engine. It tracks category scores, context-gates ambiguous signatures such as `FFFE07D1`, rejects extension-only non-XEX2 files as identity evidence, marks DLL/module XEX files as components, retains runner-up categories, and reports ambiguity instead of hiding close calls.

See [XEX Identity Fusion](docs/XEX_IDENTITY_FUSION.md).

## Revision 7 quiet metadata diagnostics

Revision 7 removes ScanView progress toasts. Only the final completion/error notification remains. The complete scan order, file list, XEX metadata, and classification evidence continue to be written to `GAME:\NebulahDebug.txt`.

## Revision 6 scan activity notifications

Revision 6 adds visible scan telemetry through the Xbox notification UI. Nebulah announces each eligible candidate root and then shows the current application/XEX at a bounded interval (first XEX and every fifth XEX) so the notification queue is not flooded.

The complete per-file sequence remains recorded in `GAME:\NebulahDebug.txt`.

## Revision 5 XEX metadata

Revision 5 adds a platform-neutral XEX2 header parser plus the Xbox file reader used by the hardware diagnostic. It extracts execution identity, image/module fields, format information, original PE name, and security-header evidence without claiming cryptographic retail verification.

The diagnostic also ships a small set of non-launchable XEX2 metadata fixtures for deterministic hardware parser checks.

See [XEX Identity Research](docs/XEX_CLASSIFICATION_RESEARCH.md).

## Revision 4 application enumeration

Revision 4 adds a one-level `ApplicationEnumerator` that operates only on temporary candidate roots classified as game libraries, homebrew roots, emulator roots, or generic application roots.

It records direct XEX files and immediate child applications, preferring `default.xex`, then `default_mp.xex`, then another discovered XEX as the temporary primary entrypoint. It never recursively crawls the full title tree and does not persist applications.

See [One-Level Application Enumeration](docs/APPLICATION_ENUMERATION.md).

## Revision 3 candidate diagnostics

The Revision 3 diagnostic XEX runs the same candidate-classification code intended for Nebulah Core. The resulting `NebulahDebug.txt` includes temporary suggestions with:

- path;
- candidate kind;
- 0-100 score;
- confidence band;
- proposed role;
- optional entrypoint;
- evidence flags;
- explicit temporary/confirmation-required state.

`default_mp.xex` is logged as strong game evidence, while plain `default.xex` remains intentionally ambiguous without supporting context.

Sensitive-looking filenames are redacted from debug directory listings.

## Revision 2 diagnostic report

The Revision 2 hardware build writes a diagnostic report beside the launched XEX:

```text
GAME:\NebulahDebug.txt
```

Because `GAME:` resolves to the directory containing the currently launched title, this does **not** assume Nebulah lives on HDD, USB0, or any other specific storage device.

The report records only development-relevant information:

- Nebulah build/version;
- Xbox system version and current title ID;
- language, region, AV pack and video-mode information;
- current launch-directory listing;
- physical HDD/USB/MU/optical probe results;
- Nebulah-owned alias mount status;
- disk-space data where available;
- shallow root directory listings for content-capable volumes;
- existence checks for likely dashboard/game/homebrew folders.

The diagnostic intentionally does **not** collect CPU keys, keyvault contents, console IDs, serial numbers, profiles, XUIDs, credentials, or account data.

## Current controls

| Control | Action |
| --- | --- |
| LB / RB | Previous / next dashboard tab |
| D-pad | Move tile focus |
| A | Activate the selected placeholder module |
| B | Return to Home |

## Dashboard design

The first UI mirrors the durable interaction structure of the later Xbox 360 Metro dashboard: horizontal categories, large content tiles, high-contrast focus, and controller-first navigation.

Nebulah Dash does **not** ship Microsoft dashboard artwork, logos, sounds, fonts, or extracted dashboard resources. The UI foundation uses simple original geometry and colors while loading the system font already present on the console.

## Repository layout

```text
Nebulah-Dash/
├── include/
│   └── nebulah/
│       └── Version.h
├── src/
│   ├── core/
│   │   ├── App.cpp
│   │   └── App.h
│   ├── platform/
│   │   ├── Xbox360Platform.cpp
│   │   └── Xbox360Platform.h
│   ├── ui/
│   │   ├── DashboardRenderer.cpp
│   │   └── DashboardRenderer.h
│   └── main.cpp
├── build/
│   └── xbox360/
│       └── xex.xml
├── docs/
│   ├── ARCHITECTURE.md
│   └── ROADMAP.md
├── tools/
│   └── build.ps1
├── NebulahDash.sln
├── NebulahDash.vcxproj
├── CHANGELOG.md
├── CONTRIBUTING.md
└── README.md
```

## Hardware-test build status

### Known-bad: libxenon Aurora-launch build

The first open-toolchain hardware build used Free60 libxenon and successfully compiled/packed into a valid XEX. Real-hardware testing showed that launching it from Aurora immediately freezes the console.

That result is now treated as a runtime-architecture failure, not a usable dashboard build. The libxenon program calls bare-metal-style Xenos and USB initialization after the Xbox 360 OS has already launched the title. **Do not use the revision-1 libxenon artifact for Aurora or DashLaunch testing.**

Known-bad artifact:

```text
Commit: 844a205204eac85776ff86f01599f59cd1152fd3
Size:   6,819,840 bytes
SHA256: d28a5af0b1ed83b6c736a3942bcb0031f8f72fa09714c075f4ddfc314ea6a4a2
Result: Immediate console freeze when launched from Aurora
```

The source remains in `platform/libxenon/` as an experimental/reference backend only.

### Current test: Revision 12 DatabaseVFS diagnostic

The active hardware-validation route is the OS-hosted diagnostic in `platform/diagnostic/`. Retail-kernel launch and return through `XamLoaderLaunchTitle(NULL, 0)` were validated on Trinity/RGH3/kernel 17559; the original XAM smoke probe is historical. This does not establish acceptance of the current database build or the full dashboard UI.

[Evidence commit `45fde37`](https://github.com/Nebulah360/Nebulah-Dash/commit/45fde3713938606b59673ceb1e28196f831b6cc2) records:

| Evidence | Revision 12 result |
| --- | --- |
| Diagnostic source commit | `f47c4e87ba087fa1c76930fa262e5ad31e1d609a` |
| Host/source checks | Passed; [source workflow 36212705506](https://github.com/Nebulah360/Nebulah-Dash/actions/runs/36212705506) |
| Xbox diagnostic build | Passed, including XEX signing verification; [build workflow 36212705490](https://github.com/Nebulah360/Nebulah-Dash/actions/runs/36212705490) |
| Artifact | `Nebulah-Dash-v0.2.0-alpha.11-rev12-DatabaseVFS` |
| XEX SHA-256 | `4a86902f83b3d63f02b60ff83c3845cadd86b952d3d77bd96543091ad8438e70` |
| Downloaded ZIP SHA-256 | `e7b1ab00baf4dbabbcf0838fb406f8524c2b43b02e88d574c752d578a0475c19` |
| Downloaded XEX hash / embedded source commit | Checked successfully |
| Fresh Xbox DatabaseVFS acceptance | **Pending** |

Build/signing verification is separate from on-console behavior. Acceptance requires fresh schema creation, transactional scanner-record writes, successful close/reopen, matching compiled/stored schema and record hashes, executable ID checks, `integrity_check=ok`, an empty foreign-key check, and row counts consistent with that scan. A second run must exercise the existing database. Preserve failed databases and journals; never delete them to hide a failure. Normal persistence stays disabled while hardware acceptance and durable storage identity remain unresolved.

These are private-core development results, not a stable release promotion. Public README synchronization does not publish development binaries or source. Core workflow/evidence links require repository access.

## Storage and path configuration

Nebulah will not assume that every console uses the same folder structure.

The first reference console has Aurora and games on the internal HDD while Nebulah, additional games, and tools are also present on `USB0:`. That is exactly the kind of mixed layout the project needs to support.

The planned model is:

```text
detect volumes
    ↓
suggest likely folders/apps
    ↓
user confirms logical roles
    ↓
validate paths
    ↓
store Nebulah configuration
```

Return-to-dashboard routing is intentionally separate from library paths. Normal exit uses the Xbox/DashLaunch dashboard route; an explicit Aurora or other dashboard executable can be stored only as a verified fallback.

See:

- [Storage Paths, Discovery, and Return Routing](docs/PATHS_AND_DISCOVERY.md)
- [First-Run Setup Wizard](docs/FIRST_RUN_SETUP.md)
- [Example path configuration](config/nebulah.paths.example.ini)

## Xbox-side storage discovery

The Xbox implementation now has a dedicated storage discovery layer.

It does not trust Aurora/XBDM/plugin drive aliases. Instead, Nebulah creates temporary project-owned aliases such as `NebHdd:` and `NebUsb0:` for physical Xbox device paths, verifies that the root is accessible, and reports only devices that actually exist.

Initial probes cover:

- internal HDD data partition;
- up to three USB mass-storage devices;
- memory-unit slots;
- known onboard-memory variants;
- optical media.

Only internal HDD and detected USB mass storage are recommended for automatic content-path suggestions by default. Removable paths are revalidated on every boot.

See [Xbox-Side Storage Discovery](docs/XBOX_STORAGE_DISCOVERY.md).

## Build requirements

### Active hardware-validation build: open-toolchain diagnostic

Revision 12 hardware testing uses `platform/diagnostic/build.sh` with `DEVKITXENON` configured and the pinned SQLite dependency verified by `python3 tools/fetch_sqlite.py`. `.github/workflows/build-discovery-diagnostic-xex.yml` defines XEX packing, signing verification and artifact hashing. Host checks are defined in `.github/workflows/source-check.yml`; they do not replace Xbox hardware acceptance.

### Full dashboard UI build: RXDK-360

The separate full UI project uses Team Resurgent's **RXDK-360** integration. Its render/input foundation is not the current DatabaseVFS hardware test.

Requirements:

1. Windows
2. Visual Studio 2022 or 2026
3. A full, legitimately obtained Xbox 360 XDK installation
4. RXDK-360 configured to use that XDK
5. An RGH/JTAG/other homebrew-capable Xbox 360 for retail-hardware testing

RXDK-360 does not redistribute the Microsoft XDK. It provides the modern Visual Studio/clang integration around the developer's own XDK installation.

### Build from Visual Studio

Open:

```text
NebulahDash.sln
```

Select:

```text
Release | Xbox 360
```

Then build the solution.

The intended executable name is:

```text
360Dash.xex
```

### Build from Developer PowerShell

From a Visual Studio Developer PowerShell with RXDK-360 installed:

```powershell
.\tools\build.ps1 -Configuration Release
```

## Installing on a test console

For Revision 12 acceptance, **do not replace your working dashboard path**.

1. Keep Aurora/FSD/XeXMenu or another known-good recovery route available.
2. Obtain the Revision 12 diagnostic from the private-core workflow and verify its embedded source commit and XEX SHA-256 against the recorded build.
3. Preserve the failed Revision 10 database and journal. Place the diagnostic in a **separate empty test folder** on accessible storage; `USB0:` is only the reference console's testing drive, never a default.
4. Launch manually from Aurora/XeXMenu and confirm normal dashboard return.
5. Retrieve `NebulahDebug.txt` beside the launched XEX and retain `NebulahLibraryTest.db` and any journal. `GAME:\` resolves to that launch directory, not a durable storage identity.
6. Check every DatabaseVFS acceptance condition above and scanner regressions, then rerun in the same folder to test reopening a valid database.
7. Keep normal persistence and DashLaunch boot replacement disabled. Full UI video/controller acceptance and recovery-tested boot replacement are separate gates.

A dashboard replacement should always retain a safe alternate boot path while under development.

## Validation matrix

| Area | Revision 12 state |
| --- | --- |
| Source structure | Complete |
| RXDK-360 project definition | Complete |
| D3D9 initialization | Implemented |
| XUI initialization | Implemented |
| Controller navigation | Implemented |
| Dashboard shell | Implemented |
| Retail-kernel launch / dashboard return | Validated on Trinity/RGH3/kernel 17559 in earlier revisions; fresh Revision 12 execution pending |
| Scanner pipeline | Revision 9 Trinity acceptance: 57 apps (51 games, 2 dashboards, 4 homebrew); regression fixture retained |
| SQLite schema / identity and record hashes | Implemented; host/source checks passed |
| Xbox SQLite VFS / repository | Implemented; host-shim integration passed; Xbox ABI/FATX/durability acceptance pending |
| Revision 12 diagnostic build / XEX signing verification | Passed; downloaded XEX hash and embedded source commit checked |
| Fresh DatabaseVFS hardware acceptance | **Pending**, including create/commit/reopen/hash/integrity checks |
| Normal persistent scanner commits | **Disabled**; hardware acceptance and durable storage identity unresolved |
| Full UI hardware acceptance / other console configurations, including JTAG | Pending; do not generalize earlier Trinity results |
| libxenon Aurora launch | **Failed: immediate freeze** |
| Original retail-kernel XAM probe | Historical bring-up test; current test is the Revision 12 diagnostic |
| Xenia smoke test | Pending |
| DashLaunch boot replacement | Not enabled |
| Filesystem writes | Diagnostic report and dedicated test DB/journal only; no normal library/configuration writes |
| Network server | Not enabled |
| Plugin loading | Not enabled |

Record new hardware results against their embedded version, revision and source commit, with console type, kernel, hack type and observed behavior. Host/source passes and signing checks must not be recorded as hardware acceptance.

## Versioning

Nebulah Dash uses semantic versioning plus a separate project revision.

Current:

```text
Version:  0.2.0-alpha.11
Revision: 12
Codename: DatabaseVFS
```

The semantic version describes feature/API maturity. The revision is a monotonically increasing milestone/build identifier.

Version constants live in:

```text
include/nebulah/Version.h
```

## Near-term development order

The immediate sequence is:

1. Run the Revision 12 DatabaseVFS diagnostic in a fresh test folder; preserve the old failed DB/journal and collect the new report/database.
2. Validate create/commit/close/reopen, schema and record hashes, integrity/foreign keys, scanner counts and a second run. Investigate any native VFS/ABI/FATX failure without discarding evidence.
3. Resolve durable storage identity and missing-media handling; preserve user favorites/history through rescans.
4. Only after those gates pass, enable normal persistent scanner commits and complete library-backed launch/UI integration.
5. Continue console information, file management, safe DashLaunch reading before editing, authenticated FTP/API services, and separately gated plugin/package systems.

Stable public promotion still requires reviewed, hardware-tested milestones and recovery evidence. A README update is documentation only.

See [docs/ROADMAP.md](docs/ROADMAP.md) for the full roadmap.

## Architecture

The foundation intentionally separates:

- Application/navigation state
- Xbox 360 platform calls
- Rendering
- Future services

Game scanning, FTP, DashLaunch management, package installation, community-service integrations, and plugins should not be implemented directly in the UI renderer.

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Credits and inspiration

Nebulah Dash is original project code, but it exists because of years of Xbox 360 homebrew work. The following projects are important references or design inspirations.

### Aurora / XboxUnity

Aurora is the clearest functional reference for the long-term dashboard feature set: game-library management, cover/content presentation, title launching, scripts, Unity/LiNK integration, FTP/NOVA-style remote functionality, and a controller-first replacement dashboard.

Nebulah Dash is **not** a fork of Aurora and does not include Aurora assets or source code.

Project: https://github.com/XboxUnity

### Freestyle Dash

Freestyle Dash is an important architectural and historical reference for Xbox 360 dashboard replacements. Its public source demonstrates proven patterns around XUI applications, content indexing, storage handling, FTP, plugins, themes, and dashboard lifecycle.

Nebulah Dash uses its own source structure and renderer.

Project: https://github.com/XboxUnity/freestyledash

### DashLaunch

DashLaunch defines much of the real-world boot and plugin workflow on RGH/JTAG consoles. Nebulah Dash plans to expose safe, validated editing of launch.ini and related settings while preserving backups and recovery behavior.

Reference: https://consolemods.org/wiki/Xbox_360:DashLaunch

### Team Resurgent RXDK-360

RXDK-360 is the toolchain integration for the full dashboard UI project. It enables Xbox 360 development through Visual Studio 2022/2026 while using the developer's own licensed Xbox 360 XDK. The active DatabaseVFS diagnostic uses the separate open-toolchain route described above.

Project: https://github.com/Team-Resurgent/RXDK360

### RXDK360 Samples

The RXDK360 sample collection is a valuable compatibility/reference set for current clang/XDK builds, including D3D9 and XUI examples.

Project: https://github.com/Team-Resurgent/RXDK360-Samples

### OpenXeChain

OpenXeChain is an important long-term project toward a free/open Xbox 360 XEX toolchain. Nebulah Dash is intentionally structured so an OpenXeChain backend can be evaluated as its graphics/UI ecosystem matures.

Project: https://github.com/OpenXeChain

### SynthXEX and xecorelib

SynthXEX provides open XEX construction work and xecorelib provides Xbox kernel/XAM import definitions for OpenXeChain. Both are important references for the future open-toolchain path.

Projects:

- https://github.com/OpenXeChain/SynthXEX
- https://github.com/OpenXeChain/xecorelib

### XboxTLS

Modern HTTPS support is essential for a future homebrew/package browser and GitHub-backed services. XboxTLS is an important reference for bringing modern TLS behavior to Xbox 360 homebrew.

Project: https://github.com/JakobRangel/XboxTLS

### X-Store

X-Store demonstrates a modern console-side homebrew discovery/download workflow and is an important UX/packaging reference for Nebulah Dash's planned package manager.

Project: https://github.com/951261/X-Store

### XboxToolkit

Team Resurgent's XboxToolkit is a useful modern reference for Xbox/Xbox 360 containers, XEX metadata, GOD content, XDBF data, and related tooling. It may inform future companion-app metadata handling.

Project: https://github.com/Team-Resurgent/XboxToolkit

## Legal / redistribution notes

This repository must not contain:

- Microsoft Xbox 360 XDK binaries
- Microsoft XDK headers or libraries
- extracted Xbox 360 dashboard assets
- Microsoft system fonts
- encryption keys
- copyrighted game content

The project may call APIs supplied by a user's properly installed development environment, but those files are not distributed here.

The original Nebulah Dash source in this repository is licensed under the MIT License unless a file states otherwise.

## Safety during development

Dashboard replacement development can strand a console in a bad boot path if tested carelessly.

Until Nebulah Dash reaches a recovery-tested milestone:

- launch it manually first;
- keep a known-good dashboard installed;
- keep DashLaunch button-bypass or alternate boot paths configured;
- avoid writing NAND;
- back up launch.ini before testing any future settings writer.

Preserve read-before-write behavior: inspect and validate existing paths/configuration before any explicitly confirmed change. Discovery suggestions remain temporary until confirmed; do not silently rewrite path configuration, DashLaunch or NAND.

## Project state

Nebulah Dash is at **0.2.0-alpha.11 / Revision 12 / DatabaseVFS** in the private development core. The scanner pipeline, SQLite schema/hash contracts and Xbox-native VFS/repository diagnostic are implemented. Host/source checks and XEX signing verification passed, while fresh Xbox DatabaseVFS acceptance is still pending. Normal persistence remains disabled; recovery, no-proprietary-assets and read-before-write rules remain in force. This documentation update does not promote development code or binaries to the public stable repository.
