# GUNDAM BREAKER 3 BREAK EDITION — launcher (CUSA07651)

A single window that takes the PS4 **PKG you own**, unpacks it into a normal game folder, sets the
graphics options and starts the game through **psport**, the PS4 translation layer used by this port.
The game's own `eboot.bin` runs natively (same x86-64 CPU); psport supplies the PS4 system libraries
and translates the GPU work to Vulkan.

Nothing about the game ships here: the PKG is the input, the game folder is the output. No game data,
saves or keys are uploaded anywhere, and the port runs offline.

- `run.sh` — open the launcher window (start here)
- `gb3-launcher.py` — the window itself (Tk, GTK 3 fallback)
- `pkg-build.sh` + `bin/ps4-pkg-tool` — PKG → game folder
- `psport/` — the game scripts (`play-gb3.sh`, `common.sh`, …)
- `psport-bundle/` — psport itself, with this port's patches and the GPU library
- `crash-report.sh` — bundle the last run's logs into one file

## Requirements

- Linux x86-64, including SteamOS 3 on a Steam Deck. A Vulkan driver is required; a GPU that can
  share host memory (`VK_EXT_external_memory_host`) is required — software rendering cannot run it.
- `python3` 3.7 or newer, with `tkinter` (most distros: `python3-tk`), or GTK 3 (`python3-gi`) instead.
- Your own GUNDAM BREAKER 3 PKG (the **base game**, not an update PKG) and its size plus about half
  again in free disk space: the unpack writes ~13 GiB.
- No installation step. Extract the archive anywhere writable; an exFAT microSD is fine, but if the
  files lost their executable bits, start it with `bash run.sh` instead of `./run.sh`.

## Quick start

1. Extract, then run `./run.sh` (or `bash run.sh`).
2. **1 . Game file** — pick your `.pkg` with **Browse…**, or **Find PKGs** to search `~/Downloads`,
   `~/Games`, your home folder and mounted drives. Press **Build native game**. The log shows the
   unpack. It writes `~/Games/PS4/CUSA07651` unless you already chose another folder, and it refuses
   to overwrite an existing build — move that folder away if you really want to unpack again.
3. **2 . Graphics** — pick the options for the next launch (see below). Defaults are sensible on a
   Deck; leave FSR off.
4. **3 . Play** — starts the game with the same bundle and settings; the launch log appears under the
   button. On a Deck, the first run after an install recompiles shaders and is not representative.

The buttons are disabled while a build or the game is running; closing the window while the game
runs does not stop the game (use psport's own menu, below).

## Graphics options

| Option | What it does |
|---|---|
| Resolution | Sets `PS_OUTPUT_RES` (the presenter/window target). The game itself still reports and renders 1920x1080 to itself, so this alone does not make the scene cheaper. |
| Frame rate | **30** is the PS4's pace and the default. **60** lifts psport's flip-rate lock to 60; **uncapped** removes the lock. On a Deck the game is GPU-bound, so neither raises the frame rate. |
| FSR 3.1 | Places the temporal upscaler on this title's scene-combine pass (`PS_UPSCALE_BEFORE_CS=11f684d5`) and renders the scene at the chosen render size. **Not usable for this game yet:** the scaled scene targets break its textures (UI shader, camera constants and bone palettes are unidentified), so leave it off. |
| Async pipelines | `PS_ASYNC_PIPELINES=1` — compiles shaders in the background, trading a possibly missing draw for fewer hitches. |
| Frame stats | `PS_FRAME_STATS=1` — writes FPS/GPU-busy to the run's log. The launch script keeps a per-run debug folder either way. |

Settings are remembered in `~/.config/gb3-launcher/settings.json` (or `$XDG_CONFIG_HOME`); nothing is
written into the game folder or the bundle.

## Playing

- **Menu: F1.** psport's overlay opens with F1, where the exit/quit, settings and diagnostics live.
  The old L3+R3 shortcut is removed in this build, so both stick clicks reach the game — GB3 uses
  them for an in-game skill.
- The game starts offline: PSN sign-in, shop and online services are stubs. GB3's English startup
  PSN question is answered **No** automatically.
- On a Steam Deck the game is GPU-bound at 1920x1080: expect a median around 25 fps with the GPU at
  its clock ceiling. That is the port's present limit, not a setting you can fix here.
- Quit through psport's F1 menu, or close the game window.

## Saves

Saves live **inside the bundle**, at
`psport-bundle/user/savedata/1/CUSA03489/` (relative to this folder). They are not in the game
folder and not in the launcher's config, so copying that directory is the backup. Keep the bundle
folder intact when moving things around.

## Command line

```bash
bash run.sh                      # the window
bash run.sh --backend gtk        # force GTK 3, or --backend tk
bash pkg-build.sh game.pkg [out] # unpack without the window
bash pkg-build.sh --selftest     # check that the bundled unpacker starts (glibc 2.34+)
bash crash-report.sh [run-dir]   # collect the newest (or given) run's logs into crash-report-*.txt
bash psport/play-gb3.sh --check  # report what the launcher would use, launch nothing
```

Environment overrides: `GAME=/path/to/CUSA07651`, `BUNDLE=/path/to/psport-bundle`,
`GB3_PKG_TOOL=/path/to/ps4-pkg-tool`.

## If something does not start

| Symptom | Fix |
|---|---|
| `python3 is required` | Install `python3` and `python3-tk` (or `python3-gi` for GTK 3). |
| Nothing happens when double-clicking `run.sh` | The executable bit is gone (exFAT). Run `bash run.sh`, or add it as a non-Steam game with `bash /path/run.sh`. |
| `play-gb3.sh was not found` | Keep the archive's folder layout intact: `psport/` must stay beside `gb3-launcher.py`. |
| `The game folder is missing` | Build from the PKG first (step 1). |
| Unpacker exits 126/127, or `glibc` message | The shipped `bin/ps4-pkg-tool` needs glibc 2.34+. Build it from source and point `GB3_PKG_TOOL` at it (see `pkg-build.sh` output). |
| Unpacker finishes but there is no `eboot.bin` | That PKG is an update, not the base game. Use the base-game PKG. |
| `libfmt.so.12: cannot open shared object file` | A copied bundle still names the build machine's loader. Run `bash psport/play-gb3.sh --check` once; it repairs `PT_INTERP` for the new path. |
| Game exits at mission end on a Deck | This build carries the Lua heap-placement fix (`PS_LOW_FLEXIBLE=1`). If it still happens, run `bash crash-report.sh` and check `faults.txt` for a fault at `0x3c0cd2` — send that report. |
| Every launch recompiles shaders | The first run after a binary change clears the shader cache; subsequent runs are warm. |

Debug folders: `~/Games/PS4/psport-debug/<host>/<timestamp>-CUSA07651/`, with `run.log`, `calls.txt`,
`env.txt`, `gpu.log` and `faults.txt`. `crash-report.sh` gathers them.

## Legal

This launcher contains no game code, no game assets and no decryption keys of its own; it unpacks a
PKG you already own. GUNDAM BREAKER 3 is © Bandai Namco Entertainment. The bundled PKG unpacker is a
build of xXJSONDeruloXx/ps4-pkg-tools (GPL-2.0-or-later, see `bin/LICENSE-ps4-pkg-tool.txt`); psport
and its libraries keep their own licenses in `psport-bundle/LICENSE` and `psport-bundle/LICENSES/`.

