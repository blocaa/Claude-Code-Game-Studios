# Sweep Bots! — Project Handoff

> Carry this file into Claude Code Desktop on your computer. It holds the game plan and the
> project status from the cloud planning session. In the local session, start with:
> "Read `design/sweep-bots-handoff.md` and continue from its Status section."

## Status & how to resume

**Done so far**
- `/start` onboarding is complete. It saved these settings to `project.yaml` on branch `chore/onboarding-config` of `blocaa/Claude-Code-Game-Studios`:
  - `project.stage: Concept` (also mirrored in `production/stage.txt`)
  - `modes.rigor: standard`
  - `modes.automation: guided`
- No engine has been configured yet. There's no game brief, no design docs and no code.

**Get it onto your PC (Windows)**
1. Clone or open your fork in a folder (e.g. `Sweepbots`):
   `git clone https://github.com/blocaa/Claude-Code-Game-Studios.git Sweepbots`
2. `cd Sweepbots`, then `git fetch origin chore/onboarding-config`, then `git checkout chore/onboarding-config`.
3. Open that folder in Claude Code Desktop (or run `claude` in a terminal there). Start Claude Code
   **inside this folder** so the studio's hooks, rules and skills load.
4. Install **Godot 4.7** (standard GDScript build) for Windows from godotengine.org.

**Next commands, in order**
1. `/brainstorm robot vacuum maze-cleaning game for kids`. Tell it to use this file as the source.
   It writes `design/game-brief.md`.
2. `/setup-engine`. Pick Godot 4.7, GDScript, iOS (portrait) target.
3. `/prototype`. Build a greybox of steering + dirt pickup + combos, then decide PROCEED/PIVOT.
4. Then continue with the path in §6 below.

**Windows + iOS note**
You can build and test the whole game in Godot on Windows. The final iOS export, signing and
TestFlight need **macOS + Xcode**. Options are a Mac (a used Mac mini is fine), a rented cloud Mac
(MacinCloud, AWS EC2 Mac), or CI with macOS runners (GitHub Actions, Codemagic). You also need an
Apple Developer account ($99/yr). None of this is needed until after the vertical slice.

---

# Game Plan: "Sweep Bots!" (working title) — iOS robot-vacuum cleaning game

## Context
Fresh Claude Code Game Studios project: no engine, no concept, `design/` and `production/` empty.
The user wants a free iOS game for kids (and families) who love robot vacuums: build/customize
a vacuum that looks like today's real robot cleaners, then clean dirt across progressive levels
with a colorful maze-chase + sweet-combo vibe. Revenue from ads.

Decisions made with the user:
- **Engine:** Godot 4.7 (GDScript), 2D, portrait, iOS export.
- **Audience:** all ages / family (not Kids Category) — but must handle under-13 players safely.
- **Ads:** opt-in rewarded ads only (no forced interstitials).
- **Core loop:** maze-cleaning + combos — original design, *inspired by* the genres, not a copy of any existing game.

## 1. Game concept
**Pitch:** "Steer your very own robot vacuum through messy rooms, slurp up every crumb, dodge the
cat, and chain sparkly combos to earn stars and unlock new bot parts."

**Pillars**
1. *My robot* — the vacuum is the child's own creation; every level rewards customization.
2. *Satisfying slurp* — every pickup pops, sparkles and chimes; cleaning a room feels great.
3. *Short and bright* — 60–120 s levels, one-finger controls, no reading required.

**Core loop:** pick level → clean the room (maze) → score stars → earn coins/parts → customize bot → next level.

## 2. Gameplay systems
| System | Summary |
|---|---|
| Movement | Swipe or virtual-joystick steering; bot glides along a tile grid like a real robot path; optional "auto-bump" turn on walls (mirrors real roombas, very readable for little kids). |
| Dirt & pickups | Crumbs (1 pt), dust bunnies (5), glitter piles, lost LEGO-style bricks, coins. Dirt fills the maze corridors — clear them all to win. |
| Combo system | Picking up dirt in quick succession builds a combo meter; color-matched dirt chains (e.g. 3 pink crumbs) burst into sparkle effects and bonus points — the "candy" feel. |
| Hazards | Wandering pets (cat, puppy), rolling socks, cables that tangle the brush, water spills; tiered by level. Being caught = lose a battery bar, not instant fail (kid-friendly). |
| Power-ups | Turbo Suction (bigger pickup radius), Mop Mode (cleans spills), Docking Charge (refills battery), Pet Treat (pets become friendly and chase-able for bonus points — the role-reversal moment). |
| Battery | Replaces "lives"/timer: battery drains slowly; return to dock for a mid-level recharge. Teaches how real robot vacuums work. |
| Stars & scoring | 1★ clean 80%, 2★ clean 100%, 3★ 100% + combo/battery target. |
| Progression | World map of houses: Kitchen → Living Room → Kids' Room → Garage → Backyard Patio → Space Station (bonus). ~15 levels per world, 90 at launch is too many — **launch with 3 worlds × 12 levels = 36**, add worlds via updates. |
| Customization | Body shape (round, D-shape, square-ish, mop-pad style), color/paint pattern, bumper, brush type, top sensor (LiDAR turret, camera), eyes/face decal, sound pack, trail effect. Cosmetic parts are mostly cosmetic; a few have small stat perks (e.g. side brush = slightly wider pickup). |
| Economy | Coins from levels → buy parts; stars gate new worlds. Rewarded ad = double coins after a level, or a free daily "mystery part" box. No paid currency in v1. |
| Save | Local save (Godot `user://` + iCloud key-value later). No accounts, no chat, no social — keeps COPPA risk low. |

## 3. Art & audio direction
- Bright, rounded, high-saturation candy palette; soft shadows; thick outlines; top-down 3/4 view.
- Vacuums modeled after real modern designs (round discs, D-shaped, LiDAR turret bump, side brushes, docking stations) — **generic, no brand logos or trademarked shapes/names** (no Roomba/Roborock/etc.).
- Juicy feedback: squash-and-stretch, particle pops, screen-sparkle on combos, cheerful "slurp" SFX, upbeat chiptune-meets-bubblegum music.
- Accessibility: colorblind-safe dirt (shape + color), large tap targets, no text-dependent gameplay, haptics toggle.

## 4. Monetization & compliance (critical for a kids audience)
- **Ad network:** Google AdMob via the Godot AdMob plugin (or AppLovin MAX) configured **child-directed / under-age treatment** for under-13 users.
- **Neutral age gate** on first launch (birth year picker, not "are you over 13?"). Under-13 → non-personalized, family-safe ad creatives only, no IDFA, no ATT prompt; 13+ → may request ATT.
- **Rewarded only:** ads are always player-initiated, never mid-gameplay; max e.g. 5/day.
- **Parental gate** (simple math question) before any link-out, settings, or future IAP.
- App Store: age rating 4+, Privacy Nutrition Label, privacy policy URL, COPPA-compliant SDK settings. Optional future: one-time "Remove Ads / Supporter Pack" IAP behind the parental gate.
- Analytics: first-party/minimal only (level completions, ad views) with no personal data.

## 5. Technical plan (Godot 4.7)
- Code root `src/`, tests `tests/unit/`, `tests/integration/` (gdUnit4).
- Scene layout: `Main` → `WorldMap`, `Garage` (customization), `Level` (TileMapLayer maze, `Vacuum`, `DirtSpawner`, `Hazard`s, `HUD`).
- Data-driven: levels as `.tres` Resources (layout, dirt mix, hazards, star thresholds); parts catalog and economy values in config Resources — no hardcoded tuning.
- Autoloads kept thin (`SaveService`, `AdService` interface with a mock implementation for tests/editor).
- Vacuum customization = layered sprites (body, bumper, sensor, decal) recolored via a palette shader.
- iOS export: Xcode on a Mac is required for the final build/signing; Apple Developer account ($99/yr). Target iPhone + iPad, 60 fps on iPhone 11-class devices.

## 6. Studio workflow (skills in this repo, in order)
1. `/start` → record concept and set rigor (recommend `standard`).
2. `/setup-engine` → Godot 4.7, GDScript, iOS target into `project.yaml`.
3. `/brainstorm` (seed with this plan) → `design/game-brief.md` with pillars above.
4. `/prototype` → greybox HTML or Godot prototype of steering + dirt pickup + combo; PROCEED/PIVOT on fun.
5. `/art-bible` → candy palette, vacuum part sheet.
6. `/map-systems` → systems index; `/design-system` for Movement, Dirt & Combo, Hazards, Customization, Economy, Ads/Age-gate.
7. `/create-architecture` + critical ADRs (ads abstraction, save format, level data format).
8. `/ux-design` for World Map, Garage, HUD, Age Gate, Parental Gate.
9. `/vertical-slice` → 1 world, 3 levels, garage with 10 parts, mock rewarded ad.
10. `/create-epics` → `/create-stories` → `/sprint-plan` → production.
11. `/release-checklist` + `release-manager` for App Store submission.

## 7. Milestones (rough, solo/small team)
| Milestone | Content | Est. |
|---|---|---|
| M0 Prototype | Steering, dirt, combo, 1 room | 2 wks |
| M1 Vertical slice | 3 levels, garage, save, mock ads | 4–6 wks |
| M2 Alpha | 3 worlds/36 levels, all hazards & power-ups, real AdMob (test IDs), age & parental gates | 8 wks |
| M3 Beta | TestFlight with families, balance pass, polish, accessibility | 4 wks |
| M4 Launch | App Store submission, privacy docs | 2 wks |

## Verification
- Unit tests (gdUnit4) for scoring, combo, battery drain, economy, star thresholds, age-gate routing.
- Retained screenshots in `production/qa/evidence/` for every UI screen (Garage, HUD, World Map, gates).
- Playtests with kids in target ages via TestFlight; `/playtest-report` to capture feedback.
- Ad compliance check: confirm under-13 path sends non-personalized/child-directed flags and never shows ATT.
- `/smoke-check` and `/gate-check` before each milestone.

