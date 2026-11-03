# Source64

**One browser library for source-built Nintendo 64 games.**

Source64 brings individual N64 browser ports into a shared player. Select a game, load its required game data, play, and return to the collection without leaving the application. The interface is branded **64 / Source**.

**[Open the game library](https://n64-decompiled-runner.steven-kaun.chatgpt.site/)**

The hosted library currently requires authorized account access. A public GitHub README does not grant access to the hosted application.

Maintained by **[TheGh0stShip](https://github.com/TheGh0stShip)**.

## About this repository

This is the documentation-only home for Source64. It contains **README.md only**. Application code, compiled engines, game data, ROMs, save files, and build artifacts are maintained outside this repository.

## Game collection

| Game | Browser implementation | Original game data |
| --- | --- | --- |
| Rugrats: Scavenger Hunt | Decompiled C compiled directly to WebAssembly; WebGL2 with a software fallback. | The existing browser port's packaged data is included. |
| Nagano Winter Olympics ’98 | Ahead-of-time recompilation using the matching decompilation metadata, including the original event overlays and audio microcode; WebGPU. | Matching USA game file, 12 MiB. |
| BattleTanx | Ahead-of-time compilation of the original 1,738-function resident program and audio microcode; WebGPU. | Matching USA game file, 8 MiB. |
| BattleTanx: Global Assault | Ahead-of-time compilation of the original 1,879-function catalogue, with the native port's compatibility adaptations; WebGPU. | Matching USA game file, 8 MiB. |

These implementation paths are deliberately identified separately. Rugrats compiles recovered C directly; the other three translate the original program ahead of time into compiled browser code. Source64 does not include a generic runtime N64 CPU interpreter, and selecting an arbitrary ROM does not create a new browser port.

The primary BattleTanx entry uses the original program's complete resident catalogue. It retains the original menus, campaign, intermissions, and multiplayer functions. The extent of playtesting is documented separately from the amount of code included.

## Shared player

- One collection and one active game at a time, with in-place loading and return to the library.
- Search, collection sorting, recently played history, and per-game details.
- Keyboard and standard gamepad support, with control guides for each title.
- Fullscreen display, mute, restart, and host pause where the engine implements it.
- Per-game save storage and supported save import/export.
- Session diagnostics and downloadable logs.
- Optional browser-local storage for remembered original game files.

Each game runs in its own same-origin frame, with its own engine, audio, workers, and save namespace. The shared shell handles navigation and session lifecycle. Returning to the library stops the current runtime and waits for supported save operations before releasing its frame.

## Getting started

1. Open the library in a capable desktop browser.
2. Select a game and choose **Play game**.
3. When requested, select the matching original USA game file.
4. Enable **Remember** to keep that file in this browser for later sessions.
5. Open **Controllers** for the active title's bindings.

The loader accepts uncompressed **.z64, .n64, and .v64** files, normalizes their byte order, and checks the full file against the expected size and SHA-1. Files selected in the player stay in the browser; the player does not upload them to a game server.

Nagano and both BattleTanx builds require **HTTPS, WebGPU, WebAssembly threads, and cross-origin isolation**. These builds initially reserve 1 GiB of WebAssembly memory. Rugrats has a lighter engine and a software graphics fallback. Mobile support and performance are not established.

The original games use a 4:3 picture. Fullscreen preserves that ratio, so widescreen displays can have side bars. The original game may also draw its own borders. Use the game's **Start** button for pause when the shared Pause control is unavailable.

## Saves

| Game | Current save support |
| --- | --- |
| Rugrats: Scavenger Hunt | Persistent Controller Pak support is not enabled in this build. |
| Nagano Winter Olympics ’98 | Game-specific **.npak** format, with validated import/export. |
| BattleTanx | Player-one raw **32 KiB .mpk** Controller Pak, with validated import/export. |
| BattleTanx: Global Assault | Player-one raw **32 KiB .mpk** Controller Pak, with validated import/export. |

Save namespaces are separate. Imported saves are validated before being applied; a module restart is used where required. Browser storage is local to that browser and site address. Saves from earlier standalone game sites do not automatically migrate to Source64; use export/import where the previous build supports it.

## Adding more games

Source64 is designed to accept additional browser ports without creating another game website.

**Permanent integration:** add a compiled game module to the application's game collection, register its metadata, and connect the shared lifecycle adapter.

**Local import:** choose **Add a game** and select a compiled browser folder containing its **n64-game.json** manifest. The package is installed into this browser and opens inside the same player. Imported builds execute on the library's origin, so use trusted builds.

The application's **Game integration guide** documents package metadata, loading, status, controls, audio, saves, and shutdown. A ROM or decompilation repository still needs a working browser engine before it can be registered.

## Verification and current limitations

The shared library and Rugrats startup, input, pause/resume, and return flow have been exercised in a browser. Scripted execution of the original BattleTanx and Global Assault programs reaches campaign gameplay. Nagano's original menus and event code have passed CPU/RSP checks, including a correction for executable event overlays delivered in chunks.

These checks do not establish completed campaigns, every Nagano event, physical multiplayer compatibility, every browser/GPU combination, or all original save-menu round trips. Headless execution and graphics-command checks are distinguished from rendered browser gameplay throughout the build reports.

A Nagano worker memory-access crash has been reported. A diagnostic build preserves the worker's original WebAssembly stack, thread details, and function offsets; the cause of that crash remains under investigation. Audio pacing and fullscreen behavior are also being improved based on browser reports. Controller X/Y turbo bindings are in development.

Detailed build evidence, source provenance, and known limitations are available through the application's integration guide and build report. The latest downloadable runtime sources and their dependency notices are maintained with the application.

## Related decompilation projects

- [Rugrats: Scavenger Hunt](https://github.com/TheGh0stShip/Rugrats-Scavenger-Hunt)
- [Nagano Winter Olympics ’98](https://github.com/TheGh0stShip/Nagano-Winter-Olympics-98-N64)
- [BattleTanx](https://github.com/TheGh0stShip/BattleTanx)
- [BattleTanx: Global Assault](https://github.com/TheGh0stShip/BattleTanx-Global-Assault)
- [BattleTanx: Global Assault PC port](https://github.com/TheGh0stShip/BattleTanx-Global-Assault-PC-Port)

Original game code, assets, names, and trademarks remain with their respective rights holders. Runtime and third-party components retain their own licenses and notices.

---

**Documentation maintained by TheGh0stShip.** This README is updated alongside Source64's browser releases; this repository remains documentation-only.
