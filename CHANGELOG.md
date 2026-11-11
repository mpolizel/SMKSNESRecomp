# Changelog

## 0.1.5 — 2026-11-11

Framework fixes from the later ports and RecompLauncher support. A setting
that needs a restart (renderer, audio output) now comes back to the same
moment instead of starting the game over; a game that reads the controller
by hand gets fresh buttons on every read; a save state loaded in another
scene is no longer drawn with the previous scene's registers. The release
carries a `recomplauncher.json` for
[RecompLauncher](https://recomplauncher.com). Normal play is unchanged (the
test run gives the same pictures, frame by frame); saves and save states from
0.1.4 keep working.

## 0.1.4 — 2026-11-03

### Changed
- A fresh install starts with **no shader** (the plain picture), the lightest
  option. A full-screen CRT shader made the game drop frames on weaker laptops.
  Pick CRT Lottes or another preset under Settings → Shader; your choice is
  still saved and kept. An install that already has a shader keeps it.
- On laptops with two graphics cards (NVIDIA Optimus, AMD switchable
  graphics), the game now asks for the **dedicated graphics card** instead of
  the integrated one.

## 0.1.3 — 2026-11-02

### New
- **Settings during play:** Ctrl+L (or Select+L3 on a controller) reopens the
  launcher. Shader, window scale, volume and controls apply when you press
  Resume, without restarting the game.
- **Audio interpolation:** press **I** to cycle Gaussian (the console's own,
  default), Cubic (crisper highs) and Sinc. It changes only what you hear;
  the game, its echo and save states are unaffected. The choice is kept.
- New upscaler shaders: **ScaleFX**, **NNEDI3 (neural)** and **NNEDI3
  (neural, high)**.
- A fresh install starts with the **CRT Lottes** shader. An install that
  already has a shader chosen keeps it.
- **Updates never cost you a save.** From the next update on, the updater
  copies your in-game saves and save states to `save-backups\<date>_before_<version>\` before
  installing, leaves save files out of the copy and puts the backup back if
  they changed anyway. Save states from earlier versions keep working, and
  every release is now checked for that before it is published.

### Fixed
- HDMA raster effects (per-line color, layer and scroll changes) started one
  line late; they now start on the line the game asks for.
- Choosing **None** as the shader during play no longer leaves a black
  screen.
- Multi-pass shaders that read the original game frame from a later pass
  (ScaleFX) no longer draw black.

## 0.1.2 — 2026-11-01

### Fixed
- **Rear-view mirror:** the gap between the two screens no longer shows
  pieces of the upper screen (hills, timer) when the lower screen is switched
  to the mirror (X).
- Display writes made at the very start of vblank (screen mode, brightness,
  HDMA) are no longer applied one frame late, which caused the mirror bug
  above and could show the previous screen's layout for a frame on a screen
  change.

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
