# SISHHIN HZ MACHINE — repository guide

JUCE 8 / C++17 guitar amp-synth plugin (VST3). Version in `CMakeLists.txt`
(`project(... VERSION x.y.z ...)`). UI is a single embedded HTML page served to a
`WebBrowserComponent`.

## Layout

- `CMakeLists.txt` — JUCE fetched via FetchContent (8.0.6). On Windows the
  WebView2 NuGet package is downloaded manually (JUCE does not fetch it).
  `juce_add_binary_data(UIData ...)` embeds `Source/ui/index.html` plus the two
  PNGs; the editor serves them from `UIData`.
- `Source/Engine.h` — all DSP. Structs: `Bq`, `Sm`, `Gate`, `PitchShifter`,
  `PitchTracker`, `GuitarAutoTune`, `GuitarSynth`, `SympString`, `SitarPedal`,
  `Boost`, `DrivePedal`, `WahPedal`, `Voice`, `Amp`, `CabModel`, `EQ`, `Engine`.
  `Engine::process()` runs the 1x front end, then a 4x oversampled drive+amp
  section.
- `Source/Params.h` — single X-macro table `X(id, name, min, max, default, step)`.
  `namespace Id` is generated from it. **Adding a param only requires one row
  here** plus UI wiring; the table drives the APVTS.
- `Source/PluginEditor.cpp/.h` — WebView2 options + `UIData` resource serving and
  the `/shz/...` JSON API (`set`, `sets`, `g`, `all`, `poll`).
- `Source/ui/index.html` — the whole UI (HTML + CSS + one `<script>`). `R`/`M`
  define ranges/metadata, `D` the control list, `PRE`/`CATS` the preset bank.
- `ui-module.js` — **legacy/unused duplicate** of an older UI. It is not
  referenced by the build and still contains the old preset/A-B bugs. Do not
  edit it; the live UI is `Source/ui/index.html`.

## Build / verify

```sh
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j4
```

- Windows needs the WebView2 NuGet step in CMake (already handled).
- Headless UI test (no plugin needed): serve `Source/ui` over HTTP and load it in
  `chromium --headless --virtual-time-budget=9000 --dump-dom`. The UI tolerates a
  missing `window.__JUCE__` (the native bridge) so it renders standalone.
- `node --check` on the extracted `<script>` catches JS syntax errors.

## Conventions / gotchas

- The UI stores selectors in JS with escaped quotes (`[data-k=\"id\"]`); when
  patching with Python use raw strings and `\\"`.
- `data-k` placeholders are replaced on mount by `mk()`; after mount a
  `[data-k=...]` selector no longer matches — target `R`/`M` metadata or add an
  id instead.
- `data-tg` placeholders are replaced by `tg()`; use `root.querySelectorAll('[data-tg]')`
  or a containing id (e.g. `#arpLights`, `.arp-bay`) for post-mount lookups.
- Arp UI lights (`arpPaint`) mirror `GuitarSynth`'s step clock: same rate, swing
  weighting on odd steps, gate ratio and 4-step accent.
- Plugin has `NEEDS_MIDI_INPUT TRUE` and `acceptsMidi() == true`; MIDI is routed
  through `SHZProcessor::processBlock` -> `engine.handleMidi`.

## GitHub

- Target repo: `SAHIN2609/hzmachiensynth1`. `.github/workflows/build.yml` builds
  and packages VST3 for Windows x64, macOS universal and Linux x64.
- **The `GITHUB_TOKEN` secret is read-only** ("Resource not accessible by
  integration" on any push/ref/workflow-dispatch, despite the API reporting
  `permissions.push=true`). Pushing and `gh workflow run` fail with 403. To land
  changes, the user must push a branch/PR manually or supply a token with
  `contents: write`.
