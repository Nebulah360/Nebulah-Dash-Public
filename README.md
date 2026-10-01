# Nebulah Dash

**Nebulah Dash** is an open-source Xbox 360 homebrew dashboard project for modified consoles. Its goal is a modern, controller-first home for games, homebrew and console tools, with reliable library management and a safe recovery path.

> **Current development version: v0.2.0-alpha.11 · Revision 12 · DatabaseVFS**
>
> Host/source checks and diagnostic XEX signing verification passed. **Fresh Xbox DatabaseVFS acceptance is pending. Normal persistence remains disabled.**

This public repository currently contains project documentation. The development revision shown here is **not a promoted stable release**; development binaries and unstable source are not published through README updates.

## Progress summary

<!-- nebulah-summary: revision=12; refreshed=2026-10-01; cadence=4 -->

Nebulah Dash now has the core pieces needed to discover and organize an Xbox 360 library: storage discovery, bounded scanning, XEX metadata inspection, application classification and grouping of related executables. Its database foundation separates games and homebrew from their physical locations and preserves user-owned library state. SQLite schema checks, deterministic identities and record hashes support consistency checks, while the Xbox-native file layer supports a dedicated database diagnostic. The current work strengthens file ownership, short-read handling and error reporting, with host tests covering transactions, reopening, integrity, rollback and corruption. The next milestone is proving this database behavior on real Xbox hardware and resolving durable storage identity before enabling everyday persistent library updates.

## Current implementation

| Area | Current state |
| --- | --- |
| Dashboard interface | D3D9/XUI foundation, controller navigation and placeholder tiles; full UI hardware acceptance remains separate |
| Storage and scanner | Storage discovery, bounded application/XEX enumeration, metadata parsing, identity fusion and application aggregation implemented |
| Library database | Schema v1 separates applications, locations, executables, scan history, storage identity and user state |
| Hash contracts | Schema digests, deterministic identities and record hashes implemented; integrity checks do not establish publisher trust |
| Xbox SQLite VFS | Native ownership, EOF and error handling implemented; production VFS/repository host tests passed |
| Normal library persistence | **Disabled** pending hardware acceptance and durable storage identity |
| Dashboard boot replacement | **Not enabled**; retain a known-good dashboard and recovery route |
| Console network server, configuration writers and runtime plugin loading | **Not enabled** |

The diagnostic writes a report and a dedicated test database/journal. That limited test activity does not enable normal library or configuration writes. Discovery suggestions remain temporary until confirmed.

## Validation and next milestone

| Check | Revision 12 result |
| --- | --- |
| Host C++98 tests and source/schema checks | **Passed** |
| Production VFS/repository integration using a host file shim | **Passed** |
| Diagnostic XEX build and signing verification | **Passed** |
| Downloaded XEX hash and embedded source commit | **Checked successfully** |
| Fresh Xbox DatabaseVFS create/commit/close/reopen acceptance | **Pending** |
| Xbox ABI, FATX durability and power-loss behavior | **Pending hardware validation** |

Results are recorded in [core evidence commit 45fde37](https://github.com/Nebulah360/Nebulah-Dash/commit/45fde3713938606b59673ceb1e28196f831b6cc2), for diagnostic source `f47c4e87ba087fa1c76930fa262e5ad31e1d609a`. Core evidence requires development-repository access. The verified diagnostic artifact is `Nebulah-Dash-v0.2.0-alpha.11-rev12-DatabaseVFS`; its XEX SHA-256 is:

```text
4a86902f83b3d63f02b60ff83c3845cadd86b952d3d77bd96543091ad8438e70
```

Build and signing checks do not establish successful console execution. The next steps are:

1. Preserve any failed database and journal, then run the verified diagnostic manually from a separate empty test folder. Keep the working dashboard available.
2. Confirm schema creation, transactional scanner-record writes, close/reopen, matching schema/record hashes and executable IDs, `integrity_check=ok`, an empty foreign-key check, and row counts matching that scan. Retain the report, database and any journal.
3. Run again in the same folder to check reopening the existing database and scanner regressions. Investigate failures without deleting evidence.
4. Resolve durable storage identity and missing-media handling before enabling normal persistence. Public stable promotion requires review, hardware acceptance and recovery evidence.

`GAME:\` means the launched XEX directory, not a durable storage identity. Drive aliases such as `USB0:` are console-specific and must never become assumed defaults.

## Long-term goals

- A replacement dashboard for Xbox 360 games, XBLA, original Xbox titles, emulators and homebrew.
- Library metadata, favorites, recent games and reliable title launching.
- Safe file management, console settings and DashLaunch configuration tools.
- Authenticated file transfer and remote-management services.
- Separately validated plugin, package and community-service integrations.

These are goals, not claims that those features are ready for use.

## Recovery and project rules

- Launch test builds manually; keep Aurora/FSD/XeXMenu or another known-good dashboard and a button-bypass or alternate boot path.
- Do not replace the working boot path before recovery-tested acceptance. Do not use experimental bare-metal libxenon builds as Aurora/DashLaunch titles.
- Preserve read-before-write behavior: inspect and validate existing paths/configuration before any explicitly confirmed change. Never silently rewrite path configuration, DashLaunch or NAND. Back up `launch.ini` before testing a future settings writer.
- Preserve failed databases, journals, favorites and history. A rescan or reassigned drive alias must not silently redirect missing media or discard user state.
- Do not redistribute Microsoft XDK binaries, headers or libraries, extracted dashboard assets, system fonts, encryption keys or copyrighted game content. Use original assets and only APIs from a properly installed development environment.
- Dashboard diagnostics must exclude CPU keys, keyvault contents, console identifiers, serials, profiles, XUIDs, credentials and account data.

## Public documentation cadence

Show **only the current development revision** on this page. Keep the version, codename, implementation status and validation gates current as revisions change.

Replace the general progress summary every **3–5 revisions**, normally every **four**. Keep one rolling summary here; detailed revision notes remain in the development changelog and Git history. Each refresh should explain what the project can do, what evidence supports it, what remains pending and the next milestone. Never carry a previous build's test pass forward as proof for a new build.

Documentation updates do not authorize syncing development source or binaries, enabling persistence, or marking a build stable.

## Credits and inspiration

Nebulah Dash uses original project code and assets. Community references include [Aurora/XboxUnity](https://github.com/XboxUnity), [Freestyle Dash](https://github.com/XboxUnity/freestyledash), [DashLaunch](https://consolemods.org/wiki/Xbox_360:DashLaunch), [RXDK-360](https://github.com/Team-Resurgent/RXDK360) and its [samples](https://github.com/Team-Resurgent/RXDK360-Samples), [OpenXeChain](https://github.com/OpenXeChain), [SynthXEX](https://github.com/OpenXeChain/SynthXEX), [xecorelib](https://github.com/OpenXeChain/xecorelib), [XboxTLS](https://github.com/JakobRangel/XboxTLS), [X-Store](https://github.com/951261/X-Store), and [XboxToolkit](https://github.com/Team-Resurgent/XboxToolkit).

Original Nebulah Dash source is licensed under the MIT License unless a file states otherwise. Third-party development tools and proprietary assets are not included here.
