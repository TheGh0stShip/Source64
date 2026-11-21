# Source64

**One browser library for source-built Nintendo 64 games.**

Source64 brings individual N64 browser ports into a shared player. Select a game, load its required game data, play, and return to the collection without leaving the application. The interface is branded **64 / Source**.

The hosted library is publicly usable at **[source64.dev](https://source64.dev/)**. Matching original game files are selected locally in the browser and are not uploaded to a game server.

Maintained by **[TheGh0stShip](https://github.com/TheGh0stShip)**.

Current player release: **1.7.0**.

## About this repository

This is the documentation-only home for Source64. It contains **README.md only**. Application code, compiled engines, game data, ROMs, save files, and build artifacts are maintained outside this repository.

The shared Source64 player/runner is distributed as the compiled browser application, but its combined source archive is not published. The game decompilations and port-specific source packages remain available for inspection: each game entry links its source package, and the related public repositories are listed below. Browser-delivered JavaScript and WebAssembly can still be downloaded and inspected; this boundary concerns publication of the maintained runner source, not an impossible claim that browser code is secret.

## Game collection

| Game | Browser implementation | Original game data |
| --- | --- | --- |
| Rugrats: Scavenger Hunt | Decompiled C compiled directly to WebAssembly; WebGL2 with a software fallback. | Matching USA game file, 16 MiB; data is prepared locally. |
| Nagano Winter Olympics ’98 | Ahead-of-time recompilation using the matching decompilation metadata, including the original event overlays and audio microcode; WebGPU. | Matching USA game file, 12 MiB. |
| BattleTanx | Ahead-of-time compilation of the original 1,738-function resident program and audio microcode; WebGPU. | Matching USA game file, 8 MiB. |
| BattleTanx: Global Assault | Ahead-of-time compilation of the original 1,879-function catalogue, with the native port's compatibility adaptations; WebGPU. | Matching USA game file, 8 MiB. |
| Wheel of Fortune | Ahead-of-time compilation using the complete matching 775-function catalogue and original audio microcode; WebGPU. | Matching USA game file, 4 MiB. |
| Sesame Street: Elmo’s Number Journey | Ahead-of-time compilation using the complete matching 973-function catalogue and original audio microcode; WebGPU. | Matching USA game file, 8 MiB. |
| Sesame Street: Elmo’s Letter Adventure | Ahead-of-time compilation using the complete matching 964-function catalogue and original audio microcode; WebGPU. | Matching USA game file, 8 MiB. |

The library also displays these clearly labeled **work-in-progress placeholders**. They are not playable browser builds yet:

| Project | Current status | Source |
| --- | --- | --- |
| Madden Football 64 | Decompilation in progress; browser engine and input requirements are not established. | [Madden-64](https://github.com/TheGh0stShip/Madden-64) |
| Sydney 2000 (Prototype) | Prototype decompilation in progress. The recovered image is a `.bin`, not a standard `.z64`; it is not hosted by Source64. | [Sydney-2000-N64-Prototype](https://github.com/TheGh0stShip/Sydney-2000-N64-Prototype) |
| Superman: The New Superman Adventures | Decompilation in progress; browser engine and input requirements are not established. | [Superman-64](https://github.com/TheGh0stShip/Superman-64) |
| Jeopardy! | In-progress decompilation; no playable browser build is provided. | [Jeopardy-N64](https://github.com/TheGh0stShip/Jeopardy-N64) |

These implementation paths are deliberately identified separately. Rugrats compiles recovered C directly; the other six translate the original program ahead of time into compiled browser code. Source64 does not include a generic runtime N64 CPU interpreter, and selecting an arbitrary ROM does not create a new browser port.

The primary BattleTanx entry uses the original program's complete resident catalogue. It retains the original menus, campaign, intermissions, and multiplayer functions. The extent of playtesting is documented separately from the amount of code included.

## Shared player

- One collection and one active game at a time, with in-place loading and return to the library.
- Search, collection sorting, recently played history, and per-game details, with stable keyboard focus as library cards update.
- Keyboard and standard gamepad support, with control guides for each title.
- Hold-to-repeat A/B on the standard controller's X/Y buttons in all seven included games.
- Fullscreen play with controls that overlay the picture and hide during play.
- In-player **Settings**, **Return to game**, mute, restart, and host pause where the engine implements it.
- Per-game save storage and supported save import/export.
- Rendering-quality defaults and per-game overrides for Nagano, both BattleTanx games, Wheel of Fortune and both Elmo games, with Balanced 2× as the default.
- An opt-in **Performance** panel and local 30-second captures for all seven games; rendering-quality comparisons where supported.
- Downloadable session logs and JSON reports, with the last crash report retained separately from a restarted session.
- Optional browser-local storage for remembered original game files.

Each game runs in its own same-origin frame, with its own engine, audio, workers, and save namespace. The shared shell handles navigation and session lifecycle. Returning to the library requests shutdown and waits up to five seconds for the game to confirm its save flush before releasing the frame. If shutdown fails or times out, the player reports that persistence is unconfirmed.

## Hosted library

The public player is deployed to **Azure Static Web Apps Free**:

**[Open Source64](https://source64.dev/)**

No Azure or GitHub sign-in is required to open and use the player. The application shell, game engines, and per-game source packages are publicly retrievable; original game files remain user-supplied and local to the browser.

The Azure deployment preserves the same-origin game frames and the isolation headers required by the threaded engines. Live checks on November 4, 2026 confirmed valid HTTPS for `source64.dev`, public access to the library and all four engine files, correct WebAssembly MIME types, downloadable per-game source packages, and `same-origin` / `require-corp` isolation headers. The deployed engine hashes match the tested release artifacts. WebGPU gameplay could not be verified in the cloud test browser because it could not obtain a GPU adapter. Hosting migration does not itself establish an audio or FPS improvement.

**Saves and remembered game files belong to their browser origin.** Before switching addresses, export supported saves from the previous installation and import them into the matching game on the new address. Remembered game files and preferences do not transfer automatically. Rugrats currently has no persistent save export.

## Getting started

1. Open the library in a capable desktop browser.
2. Select a game and choose **Play game**.
3. When requested, select the matching original USA game file.
4. Enable **Remember** to keep that file in this browser for later sessions.
5. Open **Controllers** for the active title's bindings.

The loader accepts uncompressed **.z64, .n64, and .v64** files, normalizes their byte order, and checks the full file against the expected size and SHA-1. Files selected in the player stay in the browser; the player does not upload them to a game server.

Nagano, both BattleTanx builds, Wheel of Fortune and both Elmo games require **HTTPS, WebGPU, WebAssembly threads, and cross-origin isolation**. These builds initially reserve 1 GiB of WebAssembly memory. Rugrats has a lighter engine and a software graphics fallback. Mobile support and performance are not established.

The original games use a 4:3 picture. Fullscreen preserves that ratio, so widescreen displays can have side bars. The original game may also draw its own borders. Use the game's **Start** button for pause when the shared Pause control is unavailable.

## Fullscreen and focus

Fullscreen gives the game the full available height. The title and controls overlay the picture instead of reserving space above and below it, then hide after a short period without pointer activity. Move the pointer, click or tap the picture, or press **Tab** to reveal them. Gameplay keys do not keep the controls over the picture.

The controls remain available while using shared-player pause, an open dialog or the toolbar. **Settings** can be opened directly inside the player, including in fullscreen. Closing Settings or the control guide returns focus to the game; **Return to game** provides an explicit way to restore keyboard control after using the interface.

## Rendering quality and performance

For **Nagano Winter Olympics ’98**, **BattleTanx**, **BattleTanx: Global Assault**, **Wheel of Fortune** and both **Elmo** games, open **Settings** from the library or the active player. **Default rendering quality** sets the library preference. **This game's quality** can override it for the selected or active supported game; choose **Use default** to follow the library preference again.

| Setting | Internal rendering | Use |
| --- | --- | --- |
| **Performance · 1×** | Original resolution scale. | Lowest GPU pixel workload. |
| **Balanced · 2×** | Fixed double resolution scale; the default. | More detail with a bounded rendering workload. |
| **Match window** | Integer scale based on the game canvas. | A sharper image in larger windows, with more GPU work as the scale increases. |

All modes keep the picture fitted to the available 4:3 game area. Selecting 1× does not make the game window smaller. Preferences are kept in this browser, and changes apply when the game next starts or restarts. The player distinguishes the saved preference from the quality currently running. These settings affect the six WebGPU modules; Rugrats retains its own renderer.

The original game timing, original renderer frame pacing, and framebuffer data returned to the game are preserved. Lower internal resolution can help a GPU-limited session maintain the game's intended cadence; it is not a game-speed unlock. A rendered FPS gain has not yet been measured on browser/GPU hardware.

### Performance panel and local captures

All seven games provide live measurements through **Performance**. The six WebGPU modules expose completed native renderer calls and durations, audio queues and available gap counters. Rugrats exposes measured host display-list processing and its audio queue, with unsupported native fields left unavailable. Browser callback intervals and optional browser output gaps remain separate. None of these counters is presented game FPS.

Sampling is opt-in: it runs approximately once per second while the panel is open or a recording is active, and stops when it is no longer needed. Choose **Record 30 seconds** during a repeatable scene. Closing the panel lets an active recording continue; hiding the page or ending the playing session finishes it with the appropriate reason. Captures and comparisons stay in this browser.

**Recent quality comparisons** keeps the latest capture for each quality mode for that game. Restart with another quality selection and record the same scene to make a useful comparison. Scene, duration, loading activity and other browser work affect the result; this is not an automatic or controlled FPS benchmark.

Choose **Export report** to download a JSON session report containing the available diagnostics, capture, recent comparisons and session evidence. **Session details** also offers a text log and JSON session report. Unsupported, missing or stale measurements remain unavailable rather than being displayed as a healthy zero. A reported zero belongs to a supported measurement; it does not prove that every part of the session is healthy.

Renderer-call duration includes waiting time. Browser callback intervals describe scheduling, and game-ring starvation differs from a browser output underrun: a worklet can supply silence on time while it has no game samples. These quantities are kept separate. None is labeled as presented game FPS, and browser latency estimates are not total input-to-sound latency.

### Crash evidence

The player retains the first fatal error instead of allowing a late loading, playing or save-status message to replace it. The BattleTanx adapters also preserve the original stack and source location when available, even if cleanup fails. **Download report** on the stopped-game screen keeps the available evidence.

A separate **Last crash report** in the library retains the latest failure report across game restarts, and across page reloads when browser storage is available. Starting another session does not overwrite that report with healthy status messages. It contains the evidence available when the failure occurred; diagnostics that were never collected are not invented.

## Turbo controls

All seven included games support these standard-controller bindings:

| Controller input | N64 action |
| --- | --- |
| A | Normal A press |
| B | Normal B press |
| Hold X | Repeat A at 12 presses per second |
| Hold Y | Repeat B at 12 presses per second |

Turbo runs while the button is held. Ordinary A/B presses remain available, including during a turbo release phase. Input clears when the game loses focus, becomes hidden, stops, or its controller disconnects. Click the game or choose **Return to game** after using the shared toolbar to restore focus.

These are **controller** X/Y bindings. Keyboard controls remain game-specific and are shown in **Controllers**. Additional integrated games implement their own control adapters.

## Saves

| Game | Current save support |
| --- | --- |
| Rugrats: Scavenger Hunt | Persistent Controller Pak support is not enabled in this build. |
| Both Elmo titles | Original-game persistent saves are not enabled. |
| Nagano Winter Olympics ’98 | Game-specific **.npak** format, with validated import/export. |
| BattleTanx | Player-one raw **32 KiB .mpk** Controller Pak, with validated import/export. |
| BattleTanx: Global Assault | Player-one raw **32 KiB .mpk** Controller Pak, with validated import/export. |
| Wheel of Fortune | Separate player-one raw **32 KiB .mpk** Controller Pak, with validated import/export. |

Save namespaces are separate. Imported saves are validated before being applied; a module restart is used where required. Browser storage is local to that browser and site address. Saves from earlier standalone game sites do not automatically migrate to Source64; use export/import where the previous build supports it.

## Adding more games

Source64 is designed to accept additional browser ports without creating another game website.

**Permanent integration:** add a compiled game module to the application's game collection, register its metadata, and connect the shared lifecycle adapter.

**Executable folder import is disabled on the public player.** Previously imported catalogue entries are ignored; installer calls are rejected and the old `/installed/` route is blocked. Existing browser data is preserved. New executable modules enter through reviewed catalogue integration and deployment.

The application's **Game integration guide** documents package metadata, loading, status, controls, audio, saves, and shutdown. A ROM or decompilation repository still needs a working browser engine before it can be registered.

## Verification and current limitations

The current release evidence is summarized below; older release sections retain the limits of their original checks.

The shared library and Rugrats startup, input, pause/resume, and return flow have been exercised in a browser. Scripted execution of the original BattleTanx and Global Assault programs reaches campaign gameplay. Nagano's original menus and event code have passed CPU/RSP checks, including a correction for executable event overlays delivered in chunks.

These checks do not establish completed campaigns, every Nagano event, physical multiplayer compatibility, every browser/GPU combination, or all original save-menu round trips. Headless execution and graphics-command checks are distinguished from rendered browser gameplay throughout the build reports.

### Player update 1.7.0 — audio feedback, controller menus and recovery

**Jeopardy!** joins the library as an in-progress project card linking the public decompilation. It cannot launch or accept game data. There are seven playable titles and four planned projects.

Wheel of Fortune now accounts for older pending DMA buffers in the browser FIFO. Feedback is converted to source frames and capped at the verified 464-frame synthesis bound; one VI of reserve in deeper queues avoids exposing every short host jitter gap. Original clocks, rates, audio microcode and resampling are retained. In counterbalanced browser runs, both reserved-feedback candidates have zero discarded queued audio after warmup, versus 11.05 seconds for the released baseline over approximately 177 seconds. Measured candidate production stays near 48,000 output frames/s rather than 50,859. Brief ring gaps remain; this is not a physical listening or FPS claim.

Standard-controller menus support D-pad navigation, A/select, B/back and both stick-clicks for Settings. Menu ownership suppresses game input until held controls release, preserving controller connections and independent ports. Local file selection still uses touch, mouse or keyboard. Browser DOM and synthetic-pad checks cover disconnects, resting axes and held-button cleanup; physical-controller acceptance remains separate.

WebGPU device loss now records the reason and offers manual restart with a fresh game frame. Intentional device destruction is ignored. An isolated real-browser GPU-process crash test passes failure reporting, an actual downloaded report, restart and return. Wheel’s SDL close path also quiesces its Pak writer once before quitting.

The integrated gate passes **187 tests without skips**, plus **13 packaging tests**. All **373 public deployment files** match the final release package, and all seven source downloads match their expected bytes. Only Wheel’s native Wasm changes from 1.6.4. Full campaigns, every Nagano event, original-game saves for Rugrats/Elmo, live/adaptive resolution and physical listening are not established by these checks.

### Player updates 1.6.2–1.6.4 — correctness and player reliability

Rugrats CPU search no longer retains a search location retired by another player. Original-instruction and controlled same-shell comparisons reproduce the old stall and pass with the corrected engine. Rugrats now exposes live host performance measurements; persistent original-game save export remains unsupported.

Release 1.6.4 also corrects Rugrats scene cleanup ordering. A cleanup routine could redirect the scene, but the host initialized the earlier cached scene. The old long replay failed during the transition; all three corrected native modes pass 180,000 ticks each. One Wasm function body changes, with non-code sections identical.

Nagano's browser memory trap was reproduced with a matching stack. Original JAL/JALR instructions update the return-address register; the pinned AOT output omitted that write, which an original routine later reads from a saved stack slot. Six isolated original-instruction comparisons pass after restoring PC+8 before delay slots. A real Intel WebGPU browser sequence passes 450 seconds, restart and return. This fixes the reproduced trap without changing game clocks, memory size or renderer; other historical crashes and untested events are not thereby proven fixed.

The shared player preserves first-failure identity while accepting later missing worker evidence, suppresses repeated error/log writes, counts startup deadlines in foreground time, restores focus, and warns when a restored page has no confirmed save flush. Performance captures use adapter sampling times, reset stale output baselines, retain completion/storage notices and clear live numbers on failure. Global Assault adds audio-gap/trim telemetry with exact output-equivalence checks; its native engine and scheduler are unchanged.

The integrated gate passes **175 tests with no skips**, plus **13 Azure packaging tests**. Shared-player and SDK browser checks, actual source downloads, native artifact comparisons and rendered browser runs have separate evidence. Only Nagano and Rugrats native Wasm change from 1.6.3. No measured FPS improvement or physical listening claim is made. Wheel still accumulates discarded queued audio in a long browser run; its cause and correction remain under investigation. Several titles retain audio underruns under the measured host conditions.

### Player update 1.6.1 — Elmo audio pacing

Both Elmo modules now restore the original two-slot audio FIFO admission
signal. The shared runtime had always reported ready, so these games produced
sound faster than playback and repeatedly discarded queued speech. The fix
preserves original clocks, source rates, synthesis and resampling. Other titles
retain their existing audio policies.

Original CPU/RSP comparisons at 22,050 Hz playback reduced accumulated audio
from over 10 seconds to a peak below 59 ms. Hardware WebGPU checks recorded
zero stale-audio trimming, with gameplay, restart and return still working.
Brief audio underruns remain; physical listening and exact speech/animation
sync on every device have not been established. These are audio queue results,
not presented-FPS measurements.

The integrated gate passes 147 tests with no skips, plus 13 Azure packaging
tests. All 369 served production files match the release package.

### Player update 1.6.0 — Sesame Street games

**Elmo’s Number Journey** and **Elmo’s Letter Adventure** are now playable in the existing Source64 player. Each uses its own verified local 8 MiB USA game file and its own engine/storage namespace. Their completed matching inventories cover 973 functions / 444,672 executable bytes and 964 functions / 438,160 bytes respectively. CPU programs and original audio microcode compile ahead of time; no runtime N64 CPU interpreter or hosted game media is added.

Hardware WebGPU runs on an Intel adapter rendered title screens, difficulty selection and the initial 3D Sesame Street areas for both games. Keyboard input, native restart and return to the library were checked. The speech adaptations match 356 original-instruction cases per game byte for byte, and the shared-tail unsigned compare matches 337 edge/random cases. The full gate passes 145 JavaScript tests and 13 packaging tests. These checks do not establish full-game completion, physical controllers or physical listening quality.

Both matching decompilations and the per-game browser-engine source packages remain inspectable. Both downloaded source archives are checked against every manifest hash; ROMs, saves and compiled engines are excluded from those ZIPs. Persistent original-game saves are not enabled for these integrations. The five prior game Wasm binaries are unchanged from 1.5.2.

### Player update 1.5.2 — Rugrats board-renderer correction

Rugrats mode 2 now follows the original game’s renderer call and return path for every room. The recovered C had placed that shared path inside the room-zero branch; later rooms could fall through into another mode’s object-list renderer with a null list. The correction restores the original branches and retains null-pointer checks.

Rebuilding the unmodified pinned C reproduced the previous Wasm byte for byte. The corrected build changes only the board-renderer function body; compiler constants and verified local game-data preparation remain identical. The regression fails against the old engine at the original room-2 checkpoint, then passes with the correction and progresses through rooms 0, 1 and 2 and all four player turn indices. Modes 0 and 1 also pass their original-code fixtures. The downloadable corresponding-source archive includes the repair and regression. The integrated JavaScript gate passes 141 tests and all 13 Azure packaging checks pass.

### Player update 1.5.1 — local Rugrats data

Rugrats now follows the library's local-file workflow. It verifies the matching 16 MiB USA file, normalizes .z64/.n64/.v64 byte order, removes the pinned 1,270 CPU-function ranges in browser memory and verifies the result against the exact data expected by the existing engine. Gameplay still executes in the same decompiled-C WebAssembly binary. No runtime ROM CPU interpreter or replacement emulator was added.

The hosted 16 MiB game-data image and its duplicate in the downloadable ZIP are removed. The Rugrats download now contains corresponding source only. All public source ZIPs, including nested archives, are checked for game data and compiled payloads. The sole permitted loose binary data file is Rugrats' exact 1,744-byte compiler-generated C globals, checked by hash.

The JavaScript gate passes 140 tests and the Azure packaging suite passes 13. Real local-ROM verification in all three byte orders produces exactly the original engine data without modifying the input. Fresh browser and mobile-sized contexts require local data; invalid input cannot mount the engine. Canvas2D browser checks exercise rendering, pause/resume, fresh-frame restart and return. All five game-engine Wasm binaries remain byte-identical.

The 10,000-tick original-C fixtures pass modes 0 and 1. Mode 2 hits a null data pointer at tick 8,082; the legacy data reproduces the same fault at the same tick. That issue was traced to recovered C control flow and fixed in 1.5.2, as described above; these observations preserve the 1.5.1 baseline. Physical iPhone Safari gameplay, physical audio/controllers and full-game completion are not established by these checks.

### Player update 1.5.0 — Wheel of Fortune

Wheel of Fortune joins the existing shared library as the fifth playable title. It uses the current matching decompilation at revision `1f98298869d080147d605f0981d3f7422086bb86`: 775 verified source entries covering 402,772 original code bytes. Original CPU code and audio microcode are compiled ahead of time. Browser adaptations preserve the original polling conditions and read the cartridge puzzle table from the locally supplied ROM.

A Windows Edge 157 run on an Intel Gen-12LP WebGPU adapter rendered the intro, player setup, a puzzle round, wheel spins and letter selection. The last sample at 65.884 seconds recorded 3,980 VI callbacks and 2,376 display-list submissions. These counters are not presented FPS. A separate 45.001-second CPU/RSP fixture recorded 2,099 counted display lists and nonzero audio output; its renderer discarded graphics.

Browser lifecycle checks imported a formatted 32 KiB QA Pak, restarted the native engine, downloaded an identical export, explicitly restarted again, and returned to the library. This establishes the player storage path, not all original in-game save menus. The full JavaScript gate passes 131 tests; the 10 Azure packaging tests pass. All 29 existing engine, worker and adapter files remain byte-identical to release 1.4.0.

Production HTTPS checks matched all 263 served release files, including isolation headers and Wasm MIME. A separate live-domain WebGPU run passed gameplay, the Pak round trip, explicit restart, clean return and an actual browser-source ZIP download; all 906 archive entries were inspected.

Audio underruns and stale-audio trims occurred during the hardware run, which included cold loading and shader compilation. Physical listening, steady-state audio attribution, complete games and physical multiplayer remain unverified. Nagano's separately reported memory-access crash remains unresolved. Matching decompilation and browser-engine source remain inspectable; original game files and saves are excluded from publication.

### Player update 1.4.0

The shared player releases redundant remembered-ROM buffers after a successful browser-storage write and reuses the active title's buffer when relaunching it. This avoids retaining up to 28 MiB of duplicate map entries for the three included ROM-based games after returning to the library. Unremembered files and failed-storage fallbacks remain usable for the current page. This is buffer accounting, not a measured game-FPS improvement.

File loading shows reading, checksum verification, and remembering phases. Cancelling or replacing a file dialog prevents an older request from launching the wrong game. Connection and first-frame timeouts retain their failure report; late status messages cannot revive the session. Browser-cache recovery cancels abandoned game switches, and unconfirmed shutdowns visibly warn that a save flush was not confirmed.

Expired diagnostic replies are discarded before logging or replacing newer evidence. Failed samples clear live values while keeping the last snapshot available in reports; captures exclude requests begun before recording. Imported-build replacements now explicitly abort on a synchronous storage failure, preserving the previous installation.

The integrated JavaScript suite passes **129 tests**, and all **10 Azure packaging tests** pass. Local Chromium acceptance verified Rugrats startup, pause/resume, Settings and return through its Canvas2D fallback, plus actual download-and-inspect checks for a synthetic save and crash report. Software WebGL automation stalled, so hardware rendering and physical listening remain unverified. The four native engines, original timing, audio policies, and per-game source packages are unchanged by this player release. Nagano's separately reported browser memory-access crash remains unresolved.

### Player update 1.3.1

**Audio pacing:** Nagano and BattleTanx have rebuilt native engines that account for older pending audio when timing jitter creates a deeper software queue. The original games submit a DMA buffer before querying audio length; reporting only the current buffer can hide older queued sound and lead to repeated catch-up cuts. The corrected feedback preserves the ordinary one/two-buffer result and caps deeper-backlog feedback at a separately verified safe bound for each game. Original playback rates, game clocks, resampling and audio microcode are retained. Global Assault uses a different scheduler and retains its existing feedback.

An equal-length test executing Nagano's original CPU and audio RSP code used **7,920 DMA submissions over 131.985 modeled seconds**, with **±8 ms timing jitter** and a 48 kHz output consumer:

| Measurement | Previous feedback | Corrected feedback |
| --- | ---: | ---: |
| Stale-audio cuts | 146 | 0 |
| Output audio skipped | 447,463 frames / 9.322 seconds | 0 |
| Peak queue | 111.875 ms | 43.396 ms |
| Modeled ring gaps | 0 | 0 |
| Capacity overflows or invalid synthesis lengths | 0 | 0 |

A separate original-code Nagano event test included four deliberate 50 ms production stalls. It retained four gaps totaling 159 ms, with no catch-up cuts or overflows. The correction does not conceal a real missed production deadline. BattleTanx's original-code comparison with a 300 ms consumer interruption reduced modeled gaps from 17.617 ms to zero; that long interruption still required stale-audio removal. A separate campaign run reached the first battle, executed drive/fire input and exited normally.

**Nagano quality and measurements:** fixed Performance 1× and Balanced 2× modes now separate internal rendering resolution from fullscreen display size. Match window remains available. The full 4:3 display fit, original pacing, shaders and framebuffer readback are preserved. Nagano now supports the shared Performance panel and 30-second captures. Its per-game 1× preference persisted through a browser reload; the temporary test override was restored afterward.

Nagano and BattleTanx audio diagnostics now include the count of stale-audio cuts, the largest individual cut and the longest contiguous ring underrun. Quiet performance samples omit full logs and worker stacks; complete session reports retain them. Fixed-name application assets request cache revalidation, and the new native builds have distinct versioned script, worker and Wasm URLs.

The integrated root/shared/Nagano JavaScript suites passed **67 tests**. Both rebuilt browser Wasm files and their required exports were verified against recorded source and artifact fingerprints. Deterministic queue/worklet tests cover timing jitter, smooth-timing equivalence, safe bounds, partial acceptance, flushes and cursor wrap. The source archives include the updated implementations, tests and provenance.

These results establish a controlled audio-feedback improvement. They do **not** establish audible output on a physical device, a rendered FPS gain, full campaign/event completion, or resolution of Nagano's separately reported memory-access crash. Secure-origin WebGPU rendering and listening remain to be checked on capable browser hardware.

### Player update 1.3.0

**Player polish:** fullscreen controls now overlay the game and hide during play, with pointer and Tab access to reveal them. Settings is available inside the player, **Return to game** restores keyboard focus, and library-card updates preserve keyboard selection. Both BattleTanx games support a saved quality override in addition to the library default.

A real Rugrats browser session verified a **1,363 × 936** fullscreen viewport with a complete **1,248 × 936** 4:3 canvas, using the available height. Control hiding/reveal, Tab access, pause/resume, Settings and return-to-game focus were exercised. This validates the shared layout and interactions; it does not establish native WebGPU performance.

**Session evidence:** the new opt-in Performance panel offers local 30-second captures, the latest comparison per quality and JSON report export. Missing readings remain distinct from measured zero values. The host and BattleTanx adapters retain the first fatal state, and the last crash report remains available after restarting. In that release, live native metrics covered the two BattleTanx engines; Nagano support was added in 1.3.1.

The real player completed a 30-second recorder test using a temporary module clearly labeled as synthetic game data, with actual browser scheduling observations. Its comparison and crash report survived reload; the temporary module was removed before publication. The root/shared suites passed all 53 tests. The browser automation could not retrieve the exported download, so browser export retrieval remains unverified in this environment. These checks do not establish native WebGPU or audible-device performance.

**Input delivery:** both BattleTanx browser adapters reuse their four controller packets and skip unchanged native input and connection writes. Their native state persists between writes, while keyboard input remains independently merged. Turbo timing, ordinary A/B and four-port behavior are preserved.

The actual 1.2.0 and 1.3.0 adapters were compared against identical synthetic input for ten seconds at 60 and 144 browser callbacks per second. At **144 Hz**, each game produced these results:

| Fixture | Previous input writes | 1.3.0 input writes | Previous connection writes | 1.3.0 connection writes |
| --- | ---: | ---: | ---: | ---: |
| Idle, no gamepad | 5,760 | 4 | 1,440 | 1 |
| Nineteen input changes across four ports | 5,760 | 23 | 1,440 | 1 |
| Held X turbo | 5,760 | 243 | 1,440 | 1 |

Every per-poll output trace matched the baseline. Held turbo retained all **120 press edges** at both tested callback rates. Additional adapter fixtures cover normal A alongside turbo, independent ports, axes, focus loss, hidden pages, disconnects, stop clearing, fatal cleanup failure and late status messages.

These are measured reductions in native boundary calls, **not measured CPU-time or FPS improvements**. The fixture does not exercise physical controllers or audible hardware output. Browser-health tests separately cover bounded observation, unavailable APIs, visibility, iframe clocks and audio-counter resets.

The two native JavaScript engines and WebAssembly binaries are unchanged from 1.2.0; adapter version 1.3.0 is tracked separately. This update makes no native shader, audio-policy, game-timing or framebuffer-readback change. Nagano's separately reported memory-access crash remains unresolved.

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

Detailed build evidence, source provenance, and known limitations are available through the application's integration guide and build report. The latest downloadable game-port sources and their dependency notices are linked from the application.

## Next native performance work

The pinned toolchain and native dependencies were recovered for 1.3.1, with regenerated original CPU/RSP code, rebuilt engines and recorded provenance. The audio-backlog correction is complete. The remaining priorities below require further measurement and implementation; they are not claimed as optimizations already delivered.

| Priority | Area and responsibility | Gate before accepting a change |
| --- | --- | --- |
| 1 | **Runtime and rendering measurements:** distinguish game CPU, audio RSP execution, renderer work, main-thread proxy waits, GPU execution and framebuffer readback. | Profile a repeatable hardware session with bounded measurement overhead. Current outer renderer timings include waits and cannot attribute their cost to one stage. |
| 2 | **Audio adapter allocation:** evaluate reusing conversion and resampling scratch capacity. | Establish buffer/thread ownership and lifetime, preserve exact PCM and stream phase across DMA boundaries and sample-rate changes, then compare scheduler and device playback behavior. |
| 3 | **Renderer and platform work:** optimize the stage the trace identifies, such as command batching, uploads, proxy traffic or readback. | Preserve resource ownership, ordering, original pacing and CPU-visible framebuffer effects. |
| 4 | **Renderer and player quality controls:** consider live quality changes, then adaptive resolution. | Add a safe native update/resource-retirement contract first. The current engine locks quality at startup. Adaptation needs a relevant measured signal, stable transitions and a manual override. |

A frame-rate claim will also need a defined presentation observation method. Original simulation ticks, completed game renders, GPU completion and images actually displayed are different stages. VI callbacks, browser animation callbacks and display-list counts alone cannot establish presented FPS.

The detailed performance roadmap, measurements and acceptance criteria are maintained with the application's build evidence. The public player links the inspectable per-game source packages. This public repository continues to contain documentation only.

## Related decompilation projects

- [Rugrats: Scavenger Hunt](https://github.com/TheGh0stShip/Rugrats-Scavenger-Hunt)
- [Nagano Winter Olympics ’98](https://github.com/TheGh0stShip/Nagano-Winter-Olympics-98-N64)
- [BattleTanx](https://github.com/TheGh0stShip/BattleTanx)
- [BattleTanx: Global Assault](https://github.com/TheGh0stShip/BattleTanx-Global-Assault)
- [BattleTanx: Global Assault PC port](https://github.com/TheGh0stShip/BattleTanx-Global-Assault-PC-Port)
- [Wheel of Fortune](https://github.com/TheGh0stShip/Wheel-of-Fortune-N64)
- [Madden Football 64](https://github.com/TheGh0stShip/Madden-64)
- [Sydney 2000 N64 Prototype](https://github.com/TheGh0stShip/Sydney-2000-N64-Prototype)
- [Superman: The New Superman Adventures](https://github.com/TheGh0stShip/Superman-64)
- [Jeopardy!](https://github.com/TheGh0stShip/Jeopardy-N64)

Original game code, assets, names, and trademarks remain with their respective rights holders. Runtime and third-party components retain their own licenses and notices.

---

**Documentation maintained by TheGh0stShip.** This README is updated alongside Source64's browser releases; this repository remains documentation-only.

- [Sesame Street: Elmo’s Number Journey](https://github.com/TheGh0stShip/Sesame-Street-Elmos-Number-Journey-N64)
- [Sesame Street: Elmo’s Letter Adventure](https://github.com/TheGh0stShip/Sesame-Street-Elmos-Letter-Adventure-N64)

