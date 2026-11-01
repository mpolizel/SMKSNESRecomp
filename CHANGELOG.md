# Changelog

## 0.1.2 — 2026-11-01

### Fixed
- Display writes made at the very start of vblank (screen mode, brightness)
  are no longer applied one frame late; a screen change could show the
  previous screen's layout for a frame.

## 0.1.1 — 2026-11-01

### Fixed
- **Lower screen sprites:** the track map shows the racers, and the
  rear-view mirror and the second player's screen show their karts and items.
  The game uploads a second sprite table at its mid-screen split; the renderer
  drew every line with the last table.
- **Flickering split lines:** the last line of each half no longer flickers.
- **Top of player 1's screen:** the first two lines no longer show a strip of
  the previous frame's road.

### New
- **Check for updates on start** (next to *Skip launcher on boot*, on by
  default): installs newer releases while keeping saves, save states,
  settings and your ROM selection.
- A fresh install starts with the **CRT Geom (curved)** shader.

Updating from 0.1.0: unpack the zip over your install folder once (0.1.0 has
no updater) and press **Generate & rebuild**.

## 0.1.0 — 2026-10-30

First release: a setup pack that recompiles the game from your own ROM.
Title screen and one- and two-player races (Mode 7, DSP-1 command model).
Scanline, CRT and upscaler shader presets; console-free Windows build.
