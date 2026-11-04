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
- Hold-to-repeat A/B on the standard controller's X/Y buttons in all four included games.
- Fullscreen display, mute, restart, and host pause where the engine implements it.
- Per-game save storage and supported save import/export.
- Rendering-quality choices for both BattleTanx games, with 2× as the default and a 1× Performance option.
- Session diagnostics and downloadable logs, including BattleTanx audio queues and native renderer timings.
- Optional browser-local storage for remembered original game files.

Each game runs in its own same-origin frame, with its own engine, audio, workers, and save namespace. The shared shell handles navigation and session lifecycle. Returning to the library requests shutdown and waits up to five seconds for the game to confirm its save flush before releasing the frame. If shutdown fails or times out, the player reports that persistence is unconfirmed.

## Getting started

1. Open the library in a capable desktop browser.
2. Select a game and choose **Play game**.
3. When requested, select the matching original USA game file.
4. Enable **Remember** to keep that file in this browser for later sessions.
5. Open **Controllers** for the active title's bindings.

The loader accepts uncompressed **.z64, .n64, and .v64** files, normalizes their byte order, and checks the full file against the expected size and SHA-1. Files selected in the player stay in the browser; the player does not upload them to a game server.

Nagano and both BattleTanx builds require **HTTPS, WebGPU, WebAssembly threads, and cross-origin isolation**. These builds initially reserve 1 GiB of WebAssembly memory. Rugrats has a lighter engine and a software graphics fallback. Mobile support and performance are not established.

The original games use a 4:3 picture. Fullscreen preserves that ratio, so widescreen displays can have side bars. The original game may also draw its own borders. Use the game's **Start** button for pause when the shared Pause control is unavailable.

## Rendering quality and performance

For **BattleTanx** and **BattleTanx: Global Assault**, open **Settings → Rendering quality**:

| Setting | Internal rendering | Use |
| --- | --- | --- |
| **Performance · 1×** | Original resolution scale. | Lowest GPU pixel workload. |
| **Balanced · 2×** | Fixed double resolution scale; the default. | More detail with a bounded rendering workload. |
| **Match window** | Integer scale based on the game canvas. | A sharper image in larger windows, with more GPU work as the scale increases. |

All modes keep the picture fitted to the available 4:3 game area. Selecting 1× does not make the game window smaller. Changes apply when the game next starts or restarts. These settings currently affect the two BattleTanx modules.

The original game timing, original renderer frame pacing, and framebuffer data returned to the game are preserved. Lower internal resolution can help a GPU-limited session maintain the game's intended cadence; it is not a game-speed unlock. A rendered FPS gain has not yet been measured on browser/GPU hardware.

**Session details** includes completed native renderer-call counts and timings, selected resolution, audio queue depth, underruns, deliberate stale-audio discards, and available browser audio-latency estimates. Renderer calls include waiting time; their counts are not presented frames per second. Download a session log after a problematic scene to retain this evidence.

## Turbo controls

All four included games support these standard-controller bindings:

| Controller input | N64 action |
| --- | --- |
| A | Normal A press |
| B | Normal B press |
| Hold X | Repeat A at 12 presses per second |
| Hold Y | Repeat B at 12 presses per second |

Turbo runs while the button is held. Ordinary A/B presses remain available, including during a turbo release phase. Input clears when the game loses focus, becomes hidden, stops, or its controller disconnects. Click the game after using the shared toolbar to return focus.

These are **controller** X/Y bindings. Keyboard controls remain game-specific and are shown in **Controllers**. Additional imported games implement their own control adapters.

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

### Player update 1.2.0

**BattleTanx audio:** its existing SDK binding was correct, but the browser bridge reported the entire host queue to a scheduler that needs the current audio DMA remainder. That feedback held production at 21,600 source frames per second against the game's 22,050 Hz sample rate. The bridge now tracks the current DMA, and the browser output limits exceptional stale backlogs and smooths short interruption edges.

In matched 12-second runs of the actual CPU/RSP code, with a clocked output consumer and a simulated 300 ms stall at six seconds:

| Measurement | Previous output | Corrected output |
| --- | --- | --- |
| Modeled underrun | 110.1 ms | 0 ms |
| Audio still queued at the end | 194.1 ms | 27.7 ms |
| Producer capacity drops | 0 | 0 |
| Deliberately discarded stale source frames after the stall | 0 | 6,267 |
| Graphics submissions | 350 | 350 |

The stale-frame discard lets sound catch up after a long interruption. These queue/pacing results are controlled measurements, not audible device latency or rendered FPS. A separate 19.8-second scripted campaign run completed drive/fire input and native shutdown, with a 9.8 ms ending queue and no capacity drops, but recorded 17.3 ms of modeled audio gaps. Loading and host scheduling still produce variation.

**Global Assault audio:** its different scheduler passed a 16-second clocked-consumer check with zero modeled empty time after startup, zero producer drops, and a 15.7 ms ending queue. Its feedback policy is retained. Both BattleTanx outputs receive the independently verified shared-ring race fix, bounded backlog recovery, 2 ms edge smoothing, and diagnostics. Worklet fixtures cover concurrent flushes, cursor wrap, underruns, and high-sample-rate recovery.

**Rendering:** both engines include the three quality modes above and default to Balanced 2×. This bounds internal resolution independently of the fullscreen display size. Additional native timing counters help identify expensive rendering calls without labeling them as FPS.

**Compiler evaluation:** a stronger final WebAssembly optimization reduced the test binary size by 8.46%, but four counterbalanced original-campaign runs showed mixed CPU results. Median steady process CPU cost was 212.3 ms/s with the existing link and 221.5 ms/s with the candidate. The release retains the existing optimization level because the candidate did not establish faster execution. GPU rendering was not part of that benchmark.

The shared quality control and its persistence were verified in a browser. Host and adapter checks cover applying the selected mode before native startup, restart behavior, audio diagnostics, and save/shutdown handling. Audible output and WebGPU performance still need validation on the user's device. Nagano's separate memory-access crash remains under investigation.

### Player update 1.1.0

Nagano's embedded display now clears an inherited height limit that reserved 225 pixels below the game. Browser layout checks confirm that the complete 4:3 canvas fits the available space at the reported fullscreen size, a standard desktop size, and a portrait size. Controller X/Y turbo is included across all four games.

Nagano's audio scheduler now receives the remaining length of the active audio DMA buffer. Previously, an incorrect function binding always reported zero, causing the game to overproduce sound. A matched 12-second CPU/RSP pacing test reduced the peak queue from **682.7 ms to 28.9 ms** and dropped audio frames from **95,253 to zero**. Playback consumption was modeled at the game's 44.1 kHz rate; these numbers are not a measurement of a user's browser or sound device. The browser output also smooths short interruption edges, limits stale backlogs, and includes queue/underrun/drop counters in session diagnostics. Audible quality still needs validation on browser/audio hardware.

A Nagano worker memory-access crash has been reported. The build preserves the worker's original WebAssembly stack, thread details, and function offsets; the cause of that crash remains under investigation. The audio and display corrections do not establish that this separate crash is resolved.

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
