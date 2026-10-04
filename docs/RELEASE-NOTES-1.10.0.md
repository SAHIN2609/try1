# SISHHIN HZ MACHINE 1.10.0 — Interface, Wah, MIDI & Guitar AutoTune

This release is a UI/feature pass on top of 1.9.0. No preset or DSP behaviour was
removed; new controls are additive and default to the previous sound.

## Interface / embedding

- Added a real `<!DOCTYPE html>`, `<html>`, `<head>` and `<meta charset="utf-8">`.
  This fixes the mangled glyphs (`â€¹`, `â€º`, `â€¦`, `Ã—`) that appeared in the
  WebView and keeps the arrow/ellipsis/multiplication glyphs correct.
- Added a viewport meta tag so the WebView lays out correctly on high-DPI and
  scaled displays.
- Version labels in the panel and footer now read 1.10.

## A/B compare

- Reworked A/B into two independent full snapshots. Switching slots saves the
  current slot and recalls the other, so A and B always hold real sounds.
- Both slots are seeded on load, so A/B works immediately in a fresh session.
- Added a `⇄` copy button (A→B / B→A) for the "copy then tweak" workflow and a
  live status readout next to the buttons.

## Preset selection

- Preset load now resets every effect on/off to a neutral state before applying
  the recipe, so a preset can no longer inherit a stray bypass.
- The active A/B slot is kept in sync when a preset is loaded.
- The header readout shows the preset's category while browsing.

## Wah pedal

- New HZ WAH pedal with Classic, Vocal and Funk modes.
- Position, Rate, Depth, Resonance and Mix controls; the Rate/Depth pair drives
  an automatic sweep and a MIDI expression pedal (CC11) overrides it.
- Placed after the AutoTune/drop stages and before the synth, so it behaves like
  a pedal in front of the amp.

## MIDI options

- MIDI input is now enabled on the plugin (VST3 `NEEDS_MIDI_INPUT`).
- MIDI On/Off, channel selection (1–16 or Omni), mod-wheel target
  (Wah / Filter / Drive), mod amount and a note-to-synth toggle.
- CC1 (mod wheel) drives the selected target; CC11 acts as the wah expression
  pedal. When MIDI note-to-synth is enabled an external keyboard plays the synth
  voice directly.

## Guitar AutoTune

- Added a Guitar Mode switch plus Glide, Bend Follow and Formant controls.
- Vibrato and bends are now distinguished by tracking the movement of the
  correction centre rather than a single raw-pitch difference, so bends are
  followed instead of fought while vibrato is preserved.
- Added Harmonic Minor, Minor Pentatonic and Blues scale maps.
- Added a formant high-shelf that opposes the pitch shift to keep the guitar body
  from getting thin or nasal.

## Arpeggiator

- Added Trill, Pedal and Up/Down pattern families (seven total).
- Added a Steps control (2–8) and an Accent control that emphasises downbeats.
- The played note is latched through the closed part of each gate so the pattern
  no longer stutters as the guitar note decays.

## Build

- GitHub Actions now builds and packages the VST3 for **Windows x64**, **macOS
  (universal arm64 + x86_64)** and **Linux x64**. Tagged builds publish a release
  ZIP per platform.
