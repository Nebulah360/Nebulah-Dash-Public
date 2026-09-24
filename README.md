# Nebulah Dash

**Nebulah Dash** is a new Xbox 360 homebrew dashboard project intended to become a modern replacement shell for modified Xbox 360 consoles while preserving the workflows the community relies on.

> Current version: **0.1.0-alpha.1**  
> Project revision: **1**  
> Codename: **Foundation**  
> Status: **RGH/JTAG hardware-test XEX builds successfully; hardware validation pending**

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

## What revision 1 contains

Revision 1 is deliberately small enough to validate on real hardware.

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

## Current controls

| Control | Action |
| --- | --- |
| LB / RB | Previous / next dashboard tab |
| D-pad | Move tile focus |
| A | Activate the selected placeholder module |
| B | Return to Home |

## Dashboard design

The first UI mirrors the durable interaction structure of the later Xbox 360 Metro dashboard: horizontal categories, large content tiles, high-contrast focus, and controller-first navigation.

Nebulah Dash does **not** ship Microsoft dashboard artwork, logos, sounds, fonts, or extracted dashboard resources. Revision 1 uses simple original geometry and colors while loading the system font already present on the console.

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

## RGH/JTAG hardware-test build

Revision 1 now includes a second, fully open build backend under `platform/libxenon/`. It uses Free60 libxenon for the Xenon runtime and Team Resurgent's open ELF-to-XEX packer. This backend exists specifically so early hardware boot/input testing can happen without redistributing Microsoft XDK files.

The GitHub Actions workflow `.github/workflows/build-libxenon-xex.yml` currently produces a validly debug-signed, unencrypted `360Dash.xex` intended for manual launch on RGH/JTAG-class homebrew systems.

Latest verified CI build:

```text
Commit: 844a205204eac85776ff86f01599f59cd1152fd3
Size:   6,819,840 bytes
SHA256: d28a5af0b1ed83b6c736a3942bcb0031f8f72fa09714c075f4ddfc314ea6a4a2
```

This libxenon build is a hardware smoke-test backend, not yet the final XUI dashboard renderer. It preserves the same tabs, tile labels, and controller navigation so boot/video/input can be validated before more logic is added.

## Build requirements

### Recommended build path: RXDK-360

The primary development path for revision 1 is Team Resurgent's **RXDK-360** integration.

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

For the first hardware test, **do not immediately replace your working dashboard path**.

1. Keep Aurora/FSD/XeXMenu or another known-good recovery route available.
2. Copy the built 360Dash.xex to a test folder such as:
   ```text
   HDD1:\Apps\NebulahDash\
   ```
3. Launch it manually.
4. Confirm video output.
5. Confirm controller navigation.
6. Confirm that B returns to the Home tab and the console remains responsive.
7. Only after repeated successful manual tests should DashLaunch boot-path testing begin.

A dashboard replacement should always retain a safe alternate boot path while under development.

## Validation matrix

| Area | Revision 1 state |
| --- | --- |
| Source structure | Complete |
| RXDK-360 project definition | Complete |
| D3D9 initialization | Implemented |
| XUI initialization | Implemented |
| Controller navigation | Implemented |
| Dashboard shell | Implemented |
| Hardware boot | **Pending validation** |
| RGH test | Pending |
| JTAG test | Pending |
| Xenia smoke test | Pending |
| DashLaunch boot replacement | Not enabled |
| Filesystem writes | Not enabled |
| Network server | Not enabled |
| Plugin loading | Not enabled |

Once the first XEX has been tested on hardware, the validation row and changelog should be updated with the console type, kernel, hack type, and observed behavior.

## Versioning

Nebulah Dash uses semantic versioning plus a separate project revision.

Current:

```text
Version:  0.1.0-alpha.1
Revision: 1
Codename: Foundation
```

The semantic version describes feature/API maturity. The revision is a monotonically increasing milestone/build identifier.

Version constants live in:

```text
include/nebulah/Version.h
```

## Near-term development order

The next major milestones are:

1. Hardware validate 360Dash.xex.
2. Add drive enumeration and logging.
3. Build the game/homebrew scanner.
4. Add a persistent title database.
5. Launch discovered XEX titles.
6. Add console/system information.
7. Add file management.
8. Add safe DashLaunch reading and editing.
9. Add authenticated FTP/API services.
10. Add the plugin and package systems.

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

RXDK-360 is the primary toolchain integration targeted by revision 1. It enables Xbox 360 development through Visual Studio 2022/2026 while using the developer's own licensed Xbox 360 XDK.

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

## Project state

Nebulah Dash is currently an early development project. Revision 1 is intended to establish a clean, bootable UI foundation that we can validate before adding high-risk or stateful features.
