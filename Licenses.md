# Licenses

This folder is a Linux / Steam Deck port harness for **GUNDAM BREAKER 3 BREAK EDITION
(CUSA07651)**. It is not one program, so no single licence covers all of it:

- the game is Bandai Namco's and is **not** licensed here — you supply your own copy;
- the runtime that actually runs it, [psport](https://github.com/yerasuu/psport), is
  **GPL-2.0**;
- the scripts, launcher and mods written for this project are **GPL-2.0-or-later**.

This file says what each part is, what you may do with it, and what to keep if you pass it on.
Where a licence file exists, that file governs; this is a map, not a replacement.

## At a glance

| Part | Licence | Where the terms are |
| --- | --- | --- |
| This project's own code — launcher, game scripts, eboot tools, Xbox mods, docs | GPL-2.0-or-later | this file; GPL-2.0 text in `GB3-Port-Scope/psport-src/LICENSE` |
| psport runtime (`psport`, `libpsport-gpu.so`) with this port's patches | GPL-2.0 | `GB3-Port-Scope/psport-bundle/LICENSE`, `LICENSES.md`, `LICENSES/` |
| `ps4-pkg-tool` PKG unpacker | GPL-2.0-or-later | `GB3-Launcher/bin/LICENSE-ps4-pkg-tool.txt` |
| Libraries inside the psport bundle (SDL3, FFmpeg, FreeType, glibc, …) | MIT / BSD-3-Clause / Zlib / LGPL-2.1-or-later and others | `psport-bundle/LICENSES.md`, `LICENSES/`, `THIRD-PARTY-NOTICES`, `SOURCES.txt` |
| Xbox controller artwork generated for the mods | GPL-2.0-or-later (same as this project) | `GB3-Port-Scope/xbox-controller-mod/IMAGEGEN-PROMPT.md` |
| Game code, assets, the PKG, saves, gameplay screenshots | **not licensed — all rights reserved by their owners** | see [Game content](#game-content-is-not-covered) |

## This project's own code

GPL-2.0-or-later. That covers, unless a file says otherwise:

| Path | What it is |
| --- | --- |
| `GB3-Launcher/` | `gb3-launcher.py`, `run.sh`, `pkg-build.sh`, `crash-report.sh` |
| `GB3-Port-Scope/psport/` | `play-gb3.sh`, `common.sh`, `settings.sh`, `session.sh`, capture/debug helpers |
| `GB3-Port-Scope/eboot-patch/` | `eboot-image.py`, `boot-test.py` and their README |
| `GB3-Port-Scope/xbox-texture-mod/`, `xbox-controller-mod/` | `mod.py`, the inspection/scan scripts, the generated controller art and the mod READMEs |
| `GB3-Port-Scope/*.py`, `*.sh` | helper scripts at that level (`analyze-eboot.py`, `xbox-layout.py`, …) |
| `README-LINUX-STEAMDECK.md`, `AGENTS.md`, this file, the READMEs | documentation |

The local edits to psport itself (`src/system/runtime_savedata.c`, `src/input/runtime_pad.c`,
`src/system/runtime_services.c`, `src/kernel/runtime_memory.c`, the F1 menu change) are part of
psport and stay **GPL-2.0**, like the rest of that tree. `AGENTS.md` lists every patch and why it
exists.

### What you may do

- **Run it** — for yourself, on hardware you own, with a game you own. Running it carries no
  obligations.
- **Read, study and change it** — for any purpose, including making your own port of another title.
- **Share it**, with or without changes, and **charge for it** if you like (support, media, a
  bundle). GPL-2.0 is not non-commercial.

### What you must do when you share it

1. **Give the source.** If you distribute a binary or a zip that contains this code, the recipient
   must be able to get the corresponding source under the same licence. Pointing them at this
   repository (plus the psport source below) is enough while it stays public; otherwise include the
   source or a written offer valid for three years.
2. **Keep the notices.** Ship `psport-bundle/LICENSE`, `LICENSES.md`, `LICENSES.json`, `LICENSES/`,
   `THIRD-PARTY-NOTICES`, `SOURCES.txt` and `GB3-Launcher/bin/LICENSE-ps4-pkg-tool.txt` with any
   archive you repack. Do not strip them.
3. **Say what you changed.** Modified files need a prominent notice that you changed them, with a
   date; a modified binary has to be labelled as modified, not passed off as the original build.
4. **No extra restrictions.** You may not add terms (a EULA, a DRM lock, a "no redistribution"
   clause) that take away the freedoms GPL-2.0 gives the next person.

There is no warranty, to the extent the law allows — see GPL-2.0 sections 11–12.

## psport

`GB3-Port-Scope/psport-bundle/` and `GB3-Launcher/psport-bundle/` are psport **v0.2.0** with this
port's patches, distributed under **GPL-2.0**; its own `LICENSES.md` inside the bundle is the
authoritative list. Its source is kept here:

- `GB3-Port-Scope/psport-src/` — the patched tree, including the `LICENSE` and
  `LICENSES.json`/`LICENSES/`; build notes are in `AGENTS.md`;
- upstream: <https://github.com/yerasuu/psport> (psport is itself a fork of bbport, and vendors
  shadPS4's video core and recompiler, both GPL-2.0).

Some files inside psport are under their own permissive licences (MIT, BSD-3-Clause, BSL-1.0,
Zlib, CC0-1.0) and the bundled DejaVu fonts under the Bitstream Vera licence; their notices travel
with the source and in `THIRD-PARTY-NOTICES`, and they must travel with your copy too. Nothing
GPL-3.0-only or Apache-2.0 is linked into the shipped binaries: the online module and the Vulkan
loader are deliberately left out, and the bundle uses the host's own `libvulkan.so.1` and drivers.

If you publish `gb3-deck.zip` or `GB3-Launcher.zip`, you are redistributing psport, so the four
rules above apply to it. Keeping those zips' `LICENSE`/`LICENSES*`/`NOTICES` entries untouched and
linking this repository as the source satisfies them.

## ps4-pkg-tool

`GB3-Launcher/bin/ps4-pkg-tool` is a build of
[xXJSONDeruloXx/ps4-pkg-tools](https://github.com/xXJSONDeruloXx/ps4-pkg-tools)
(`BUILD_PKG_QT_GUI=OFF`), **GPL-2.0-or-later**, whose PKG decryption code comes from shadPS4. The
full text and build details ship in `GB3-Launcher/bin/LICENSE-ps4-pkg-tool.txt`. Rebuild it from
upstream if the shipped binary will not start on your distribution, and point `GB3_PKG_TOOL` at
your build.

## Libraries in the bundle

Linked into psport and present as shared objects in `psport-bundle/lib/`; each keeps its own terms,
listed per file in `psport-bundle/LICENSES.md` and reproduced in `THIRD-PARTY-NOTICES` with source
URLs in `SOURCES.txt`:

| Library | Licence |
| --- | --- |
| SDL3 | Zlib |
| FFmpeg (libavcodec, libavformat, …) | LGPL-2.1-or-later (psport's build: no GPL, v3 or network code) |
| FreeType | FreeType Licence / GPL-2.0 |
| glibc (the bundle's own loader and libraries) | LGPL-2.1-or-later |
| fmt | MIT |
| LibAtrac9 | MIT |
| AMD FidelityFX / FSR-Vulkan sources | MIT |
| sirit, xbyak | BSD-3-Clause |
| Dear ImGui, half, RenderDoc header | MIT |
| DejaVu fonts | Bitstream Vera + public-domain DejaVu changes |

## Game content is not covered

Nothing here grants any right to GUNDAM BREAKER 3. The PKG, the dumped `CUSA07651` folder, its
`eboot.bin`, textures, fonts, audio, videos, trophies and save data are © Bandai Namco
Entertainment (PS4, PSN and Orbis are Sony's; Xbox is Microsoft's). Specifically:

- **Do not redistribute the game.** The `.pkg` in this folder and the extracted game are for your
  own use on hardware you own. Do not commit them to a repository, upload them, or bundle them with
  a release — this is also why a GitHub repo cannot hold them (the PKG alone is ~13 GiB).
- **Saves are yours, not the project's.** `psport-bundle/user/savedata/` is personal data; keep it
  out of anything you publish.
- **The mods only edit your copy.** `xbox-texture-mod/mod.py` and `xbox-controller-mod/mod.py`
  rewrite textures inside your own game archive and keep a backup; they do not contain game assets.
- **Screenshots and captures** in `_shots/`, the mod folders and the READMEs show the game's
  artwork. They are fine as documentation of the port; they are not licensed for reuse as game
  assets, and they are not covered by GPL-2.0.
- No decryption keys of the project's own are included: `ps4-pkg-tool` unpacks a PKG you already
  own with the keys that tool already implements.

## Checklist if you publish a build

- [ ] `psport-bundle/LICENSE`, `LICENSES.md`, `LICENSES.json`, `LICENSES/`, `THIRD-PARTY-NOTICES`,
      `SOURCES.txt` all present and unmodified.
- [ ] `bin/LICENSE-ps4-pkg-tool.txt` present.
- [ ] A way to get the source: this repository, or the psport tag plus the patches listed in
      `AGENTS.md`, or a three-year written offer.
- [ ] Your changes marked in the files you changed and described in the release notes.
- [ ] No game files, no saves, no keys, no DRM or extra licence terms.
- [ ] `unzip -t` clean, and the README inside the zip still tells people these terms apply.

## Warranty, names and affiliation

The software is provided **as is**, without warranty of any kind, express or implied, including
merchantability or fitness for a particular purpose; the authors are not liable for damage, data
loss or a console/PC left in a bad state. GUNDAM BREAKER 3, PlayStation, PSN, Xbox and Steam are
trademarks of their respective owners. They are named here only to say what this port runs on; this
project is not affiliated with or endorsed by any of them, and the port ships no game data.

Provenance of each change and the measurements behind it are in `AGENTS.md`; the port's history and
the older shadPS4 route are in `README-LINUX-STEAMDECK.md` and `GB3-Port-Scope/README.md`.
