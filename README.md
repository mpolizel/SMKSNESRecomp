# Super Mario Kart Recomp

A native recompilation of **Super Mario Kart** (USA), built on
[snesrecomp](https://github.com/RetroPortingToolKit/snesrecomp).

Run Super Mario Kart natively on Windows.



> **You must legally own a copy of the game.** No ROM data is distributed with
> this project, in the repository or in any release. The recompiled C is
> generated locally from your own copy and is never committed.

## Download (ready-to-run Windows build)

Get the latest `supermariokart-<version>-windows-x64.zip` from the
[Releases page](https://github.com/mpolizel/SMKSNESRecomp/releases/latest).

The zip is a **setup pack**: it contains the launcher, the recompiler and this
source tree, but no game data. On first run:

1. Unzip anywhere and start `SuperMarioKartSNESRecomp.exe`.
2. Let it download the portable build tools (one time per machine), or point
   it at a `cmake-clang-v1` zip you already have.
3. Pick your own `Super Mario Kart (USA).sfc` (CRC32 `cd80db86`).
4. Press **Generate & rebuild**. The playable game is built into `build/` and
   started; run it from there afterwards.

In the launcher, **Settings → Shader** lists the scanline, CRT and upscaler
presets; choosing one switches the renderer to OpenGL automatically.

## Status

Boots to the title screen and runs one- and two-player races (Mode 7 with
the DSP-1 command model, no firmware needed); the lower screen's track map,
rear-view mirror and second player show their sprites. Most code still runs
through the snesrecomp interpreter; full cups and Battle Mode are not yet
verified. See [Porting from here](#porting-from-here).

### Framework patches

`patches/` holds two changes to the `snesrecomp` submodule. Reapply them after
updating the submodule:

```sh
git -C snesrecomp apply ../patches/snesrecomp-shader-forces-opengl.patch
git -C snesrecomp apply ../patches/snesrecomp-raster-oam-and-hdma.patch
```

The second fixes the split screen. The game uploads a second sprite table
(OAM) at its mid-screen split and the renderer, which draws after the CPU
frame, used the last table for every line: the lower screen lost its map
icons, mirror and second-player sprites. The renderer now keeps each OAM
table with the beam line it takes effect on. It also files a display write
made in the first half of a line with that line (the split's force-blank
write jitters across a line start, which made the last line of each half
flicker), and initializes HDMA channels enabled in vblank before line 1
(the top two lines showed the previous frame's road).

## ROM identity

| | |
|---|---|
| File | `Super Mario Kart (USA).sfc` |
| Publisher | — |
| Developer | — |
| Year | — |
| Mapping | hirom |
| Region | USA (North America) |
| Coprocessor | DSP |
| CRC32 | `cd80db86` |
| SHA-256 | `2ada8919688087be60a6a48cace8f877add60c45d2e5d09e2442faa55be62a49` |

`tools/regen.sh` refuses to run against anything else, so a mismatched
revision fails immediately instead of producing subtly wrong output.

## Build

```sh
git submodule update --init --recursive
bash tools/regen.sh --rom /path/to/Super Mario Kart (USA).sfc
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
```

The ROM does not have to live in the repository — keeping it on your own
drive is the better habit, and `SNESRECOMP_ROM` sets the path once for a
shell. Without either, `tools/regen.sh` finds `Super Mario Kart (USA).sfc` at the repo root,
then the path the setup wizard recorded in `rom.cfg`; `.gitignore` blocks
both from ever being committed.

`tools/regen.sh` verifies the ROM, generates `src/gen/*.c`, and re-syncs
`recomp/funcs.h`. Re-run it whenever you change anything under `recomp/`.

## Run

```sh
./build/SuperMarioKartSNESRecomp                     # launcher picks the ROM
./build/SuperMarioKartSNESRecomp /path/to/Super Mario Kart (USA).sfc # or name it and skip the launcher
```

With no ROM on the command line the build opens the recomp-ui launcher: a ROM
picker with a verification badge, plus display / audio / input settings. Your
choice is cached in `rom.cfg` beside the executable, so the next launch opens
on it and "Skip launcher on boot" makes it immediate. If the launcher is not
built in (`--no-recomp-ui`), the host falls back to a native file picker.

Either way the ROM is checked against the digests above before anything boots
— a dump that could not have produced this build is refused at the door rather
than mis-executing ten frames in.

## Layout

| Path | What lives there |
|---|---|
| `recomp/` | Analysis input: `bank*.cfg`, `symbols.toml`, generated `funcs.h` |
| `rom_identity.txt` | ROM digests — read by the build, `tools/regen.sh` and CI |
| `src/` | Host code you own: `main.c`, `game_rtl.c` |
| `src/gen/` | Generated C. Never committed — regenerate locally |
| `snesrecomp/` | Framework submodule (owns `lib/recomp-net`, `lib/retcomm-rbengine`) |
| `tools/` | `regen.sh` — the ROM → C pipeline |
| `scripts/` | `package_release.sh` — player-facing zip |
| `framework_pins.txt` | Exact framework commits this project was scaffolded against |

## Porting from here

The scaffold stops where the game-specific work starts. In rough order:

1. **Make it boot.** `src/game_rtl.c` holds the frame driver. It starts on
   the framework's beam-aligned driver (`snesrecomp/runner/src/beam_frame_driver.h`):
   one frame per PPU field, interrupts taken where the beam latches them,
   the field rasterized from the raster journal. A title whose main loop it
   does not fit replaces those calls with its own driver.
2. **Name things.** Add entries to `recomp/symbols.toml` as you identify
   routines, then re-run `tools/regen.sh`. Set `emit = true` to promote one
   into ahead-of-time analysis; leave it false to keep it interpreted.
   Regen synchronizes the marked blocks in each bank cfg, creating missing
   configs. Only proven variants become AOT code. See `recomp/README.md`.
3. **Resolve dispatch misses.** After every run, deal with unresolved
   indirect targets before anything else — they are the reason a port
   diverges, and they are cheap to fix early.
4. **Never synthesise a result** to get past uncovered code, and never edit
   `src/gen/` by hand. Fix the config or the framework and regenerate.

## Multiplayer

Two players, one controller per port.

## License

This project's own source is under the MIT license in `LICENSE`. The
framework carries its own terms (PolyForm Noncommercial 1.0.0) — see
`snesrecomp/LICENSE` and `snesrecomp/THIRD_PARTY_ATTRIBUTION.md`; the change
in `patches/` applies to that framework and falls under its terms. The
shaders under `assets/shaders/_libretro/` come from libretro/glsl-shaders and
keep each author's license in their file headers. None of these cover the
game data, which is not distributed here.
