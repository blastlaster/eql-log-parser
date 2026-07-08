# EQL Log Reader — v1.1

**Release date:** July 8, 2026

Follow-up release: a redesigned Session Report dashboard, a Launcher quality-of-life fix, a combat-tracking accuracy fix, and packaging scripts for cutting future installers.

## What's new in v1.1

**Session Report — Overview tab redesigned into a real dashboard.** Previously the report spread its data across nine tabs (Overview, Graphs, Damage by Ability, Healing by Ability, Sessions, Stance/Invocation, Spells Cast, Passive Healing (est.), Unrecognized lines), and the top bar's Session/Theme controls and buttons could get squeezed off the window at smaller sizes. Now:
- The **Overview** tab is a single scrollable dashboard: headline stat cards (session length, avg DPS/DTPS, damage, healing, kills, deaths, biggest hit, stance), a color-coded damage-split bar, a damage-taken breakdown, top damage/healing abilities as bar charts, a DPS-per-fight chart, and the Stance/Invocation performance tables — most of a session at a glance.
- Everything else is consolidated into three further tabs: **Abilities** (full sortable damage/healing-by-ability tables with category filter + search, and spells cast), **Sessions** (session-vs-session comparison, personal records), and **Diagnostics** (passive healing estimates, unrecognized-line calibration).
- The top bar is now two rows specifically so the Session/Theme dropdowns and Refresh/Change log buttons can never be squeezed off-window.

**Launcher — auto-restart on character switch.** Friends Overlay and DPS/HPS Meter previously kept watching whichever log file they were started with, so switching your active character in the Launcher required manually closing and reopening both tools. They now auto-restart with the newly selected log the moment you pick a different character (via Select or Browse). Session Report is deliberately left alone, since it's a multi-tab report you're actively reading rather than a live tracker.

**Launcher — Neon HUD removed from the Launcher's own theme picker.** Neon HUD is a fully transparent, chroma-keyed theme meant to float over the game; it never made sense on the Launcher's normal decorated window (Session Report already excluded it for the same reason). It's unaffected everywhere it's actually useful — Friends Overlay and DPS/HPS Meter still offer it.

**Combat tracker — Lifetap-family spells now count as healing.** Lifetap, Lifespike, Lifedraw, Siphon Life, and Spirit Tap deal damage and heal the caster for the same amount, but the game only ever logs the damage half of *your own* casts — confirmed against real gameplay logs, where other players' self-casts of the same spells log both halves, always healing for exactly the damage dealt. The tracker now records a matching self-heal (1:1 with the logged damage) whenever one of these spells lands, so they show up correctly in HPS and the healing-by-ability table instead of vanishing.

**Packaging.** Added `build_exe.bat` (PyInstaller, onedir build of all four tools via `eql_suite.spec`), `make_installer.bat`, and `installer.iss` (Inno Setup) for producing `EQL-Log-Reader-Setup.exe`. `make_installer.bat` locates `ISCC.exe` across Inno Setup 5/6/7, including Inno 7's per-user `%LocalAppData%\Programs` install location. See `BUILDING.md` for the full build workflow.

## What's included

**Launcher** — control panel. Auto-detects every character in the default Daybreak install, pick one, then start/stop each overlay with a click. Friends Overlay and DPS/HPS Meter now follow you automatically when you switch characters.

**Friends Overlay** — live friends list with level, class combo, race, zone, and AFK detection. Per-character rosters persist between sessions. Non-friend `/who` searches never pollute the roster.

**DPS/HPS Meter** — retro live combat meter: DPS, HPS, DTPS with melee/spell/song/damage-shield splits, damage sources split six ways (Melee / Skill / Spell / Song / DS / Pet), pet tracked as its own actor, accuracy/crit/biggest-hit, kill rate, stance & invocation tracking, and a persistent ALL TIME block. Right-click for themes, layout, fight-average vs rolling windows, DPS vs DPM, combat timeout, size, and opacity.

**Session Report** — a one-page dashboard (headline stats, damage-split and damage-taken charts, top abilities, DPS-per-fight, Stance/Invocation performance) plus Abilities (full sortable damage/healing tables, spells cast), Sessions (session-vs-session comparison, personal records), and Diagnostics (passive healing estimates, unrecognized-line calibration) tabs.

## Themes

One shared theme set across all four applets, with **16-bit Window** as the default: 16-bit Window, CRT Terminal, Arcade LED, Vintage (text-only rows), and Neon HUD — a fully transparent mode where black-outlined neon text floats directly over the game. Session Report and the Launcher render/offer it as a plain dark palette or not at all (Neon HUD is excluded from the Launcher's theme picker as of v1.1, and was already excluded from Session Report's), since neither is a floating overlay. Friends Overlay and DPS/HPS Meter offer the full set including Neon HUD.

## Distribution

v1.1 ships as four standalone Windows executables (Launcher, Friends Overlay, DPS/HPS Meter, Session Report) sharing one runtime folder — no Python install required. Keep the whole installed folder together; the Launcher starts the others as sibling processes, and each tool keeps its settings/rosters/personal-records JSON files next to itself.

## Installation

**Option 1: Download (recommended)**
Download `EQL-Log-Reader-Setup.exe` from this release and run it. It installs the Launcher and all three overlay tools together in one folder, with a Start Menu entry and optional desktop icon — no Python required. Launch the app via the Launcher shortcut it creates.

**Option 2: Build from source**
Requires Python 3.8+ and PyInstaller (`pip install pyinstaller`), and Inno Setup if you want to produce a Setup.exe of your own. From the project folder:

```
build_exe.bat        REM builds all four tools into dist\EQL Log Reader\
make_installer.bat   REM packages that into Output\EQL-Log-Reader-Setup.exe
```

See `BUILDING.md` for details and troubleshooting. `dist\EQL Log Reader\eql_launcher.exe` also runs standalone without building the installer.

## In-game setup (Friends Overlay)

**Before anything else, turn on logging:** type `/log on` in any in-game chat window. This is what makes the game write `eqlog_<Name>_<Server>.txt` in the first place — every tool in this suite reads that file, so nothing here works until logging is on. Logs are written to your EverQuest Legends install's `Logs` folder.

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

Log-line formats were calibrated against real gameplay logs; anything the parser doesn't recognize lands in the "Unrecognized lines" tab (Session Report) or the meter's calibration window rather than being silently dropped. Stance/invocation effects are sourced from eqlwiki.com; spell magnitude estimates use classic-era EQEmu reference math and are labeled as estimates in the UI. Lifetap-family self-heals (new in v1.1) are estimated 1:1 from the spell's own logged damage, since the game never logs your own self-heal from these directly. There is no client-side gear/item-stat data source, so stance/invocation switch detection is regex-based against log text and not independently verified against item data.

## License

MIT — see LICENSE.

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
