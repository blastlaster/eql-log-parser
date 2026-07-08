# EQL Log Reader — v1.1

**Release date:** July 8, 2026

Follow-up release focused on the Session Report's UI, one Launcher quality-of-life fix, and a combat-tracking accuracy fix.

## What's new

**Session Report** — the Overview tab is now a proper dashboard: headline stat cards, a color-coded damage-split bar, damage-taken breakdown, top damage/healing abilities as bar charts, a DPS-per-fight chart, and the Stance/Invocation tables, all on one scrollable page. The rest of the data (full sortable ability tables, spells cast, session comparison, diagnostics) is consolidated into three further tabs (Abilities / Sessions / Diagnostics) instead of nine. The top bar is now two rows so the Session/Theme controls and buttons can never get squeezed off the window at smaller sizes.

**Launcher** — Friends Overlay and DPS/HPS Meter now auto-restart with the newly selected log file when you switch characters (via Select or Browse), instead of continuing to watch the old character until manually closed and reopened. The Neon HUD theme (transparent, overlay-only) is no longer offered in the Launcher's own theme picker, since the Launcher is a normal window, not an overlay — it remains available for the Friends Overlay and DPS/HPS Meter.

**Combat tracker** — Lifetap-family spells (Lifetap, Lifespike, Lifedraw, Siphon Life, Spirit Tap) now show up as a healing ability. The client logs the damage half of your own casts but never a heal line for it (confirmed against real logs — other players' self-casts do log both halves, always healing for the same amount as the damage dealt); the self-heal is now estimated 1:1 from the logged damage.

**Packaging** — added `build_exe.bat`, `make_installer.bat`, and `installer.iss` for producing a distributable `EQL-Log-Reader-Setup.exe` via PyInstaller + Inno Setup, including detection of Inno Setup 7's per-user install location.

---

# EQL Log Reader — v1.0

**Release date:** July 7, 2026

First public release. EQL Log Reader is a family of always-on-top overlay tools for **EverQuest Legends**, driven entirely by the game's own log file. No injection, no memory reading, no game files touched — the tools just tail the log the game already writes, so they're safe to run alongside the game.

Requires the game in Windowed or Borderless-Windowed mode (true exclusive fullscreen draws over everything, including overlays).

## What's included

**Launcher** — control panel. Auto-detects every character in the default Daybreak install, pick one, then start/stop each overlay with a click. Re-running auto-detect always reflects exactly what's in the game folder.

**Friends Overlay** — live friends list with level, class combo, race, zone, and AFK detection. Per-character rosters persist between sessions. Non-friend `/who` searches never pollute the roster.

**DPS/HPS Meter** — retro live combat meter: DPS, HPS, DTPS with melee/spell/song/damage-shield splits, damage sources split six ways (Melee / Skill / Spell / Song / DS / Pet), pet tracked as its own actor, accuracy/crit/biggest-hit, kill rate, stance & invocation tracking, and a persistent ALL TIME block. Right-click for themes, layout, fight-average vs rolling windows, DPS vs DPM, combat timeout, size, and opacity.

**Session Report** — damage/healing by ability with category filter and search, bar-chart graphs, session-vs-session comparison with best-session stars, persistent personal records, stance/invocation performance, spells cast (with mana/cast/recast from `spells_us.txt`), and an unrecognized-line calibration tab.

## Themes

One shared theme set across all four applets, with **16-bit Window** as the default: 16-bit Window, CRT Terminal, Arcade LED, Vintage (text-only rows), and Neon HUD — a fully transparent mode where black-outlined neon text floats directly over the game (the report and launcher render it as a plain dark palette). The Friends overlay's old Classic Slate look was retired; saved "classic" settings fall back to the default.

## Distribution

v1.0 ships as four standalone Windows executables (Launcher, Friends Overlay, DPS/HPS Meter, Session Report) — no Python install required. Keep all four `.exe` files together in one folder; the Launcher starts the others as sibling processes, and each tool keeps its settings/rosters/personal-records JSON files next to itself.

## Installation

**Option 1: Download**
Download `EQL Log Reader Setup.exe` and run it. It installs the Launcher and all three overlay tools together in one folder — no Python required. Launch the app via the Launcher shortcut it creates.

**Option 2: Build from source**
Requires Python 3.8+ and PyInstaller (`pip install pyinstaller`). From the project folder:

```
pyinstaller --onefile --windowed --icon=icon.ico eql_friend_overlay.py
pyinstaller --onefile --windowed --icon=icon.ico eql_dps_meter.py
pyinstaller --onefile --windowed --icon=icon.ico eql_session_report.py
pyinstaller --onefile --windowed --icon=icon.ico eql_launcher.py
```

All four `.exe` files land in `dist/`. Keep them together in that one folder and run `eql_launcher.exe` to start.

## In-game setup (Friends Overlay)

The Friends Overlay reads `/who` and friend-list output from the game's log, so it needs a dedicated chat tab plus a macro/hotkey that refreshes that data automatically.

1. Open any chat window and create a new tab.
2. Route all `/who` messages and "Other" messages to that tab.
3. Turn off highlighting on new messages for that tab, so it doesn't flash/alert.
4. Press `L` to open Socials.
5. Create a new macro: `/friend | /who friend all | /pet who leader`.
6. Place the macro in the last slot of your main hotbar (slot 12) — any slot works, this is just what the rest of these steps assume.
7. Press `Alt+O` to open Settings, then go to Controls > Hotbar 1 > Button 12 (or whichever slot you used).
8. Rebind that button to one of your movement keys (e.g. Right / D).
9. Pressing that movement key now also fires the macro into the hidden chat tab, refreshing friend/pet data every time you move that direction.
10. Press that direction any time you want to update the friends list. `/who` results also pop up in their own window — right-click the main overlay element and give it a try.

## Notes on accuracy

Log-line formats were calibrated against real gameplay logs; anything the parser doesn't recognize lands in the "Unrecognized lines" tab (Session Report) or the meter's calibration window rather than being silently dropped. Stance/invocation effects are sourced from eqlwiki.com; spell magnitude estimates use classic-era EQEmu reference math and are labeled as estimates in the UI. There is no client-side gear/item-stat data source, so stance/invocation switch detection is regex-based against log text and not independently verified against item data.

## License

MIT — see LICENSE.
