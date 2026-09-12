# Blackout Protocol

A complete, standalone Tower Defense game. No content, vocabulary, logic, or interface reused from any other project — this repository contains only Blackout Protocol, independent from the start (new local folder, new GitHub repository, no shared history with anything else).

Visible name on every screen: **Blackout Protocol** only. No subtitle, no second name, no mention of any internal codename.

## Files

- `index.html` — English version (primary, self-contained: HTML + CSS + JS inline, zero external dependencies, no build step, no server required)
- `fr/index.html` — French version, exact functional mirror (same map, same rules, same mechanics, same effects, same screens, same difficulty, same progression — only the text is translated)
- `README.md` — this file

Open either file directly by double-clicking it in a browser. Both work offline.

## Locked rules (implemented exactly as specified)

- 5 districts, 3 global lives, 12×7 grid, 15-tile fixed path, 28 buildable tiles, 5 checkpoints
- 5 nights × 5 waves = 25 waves total, free build phase before every wave, each wave started manually (never auto-skipped), 120s active-time budget per night
- Victory: wave 25 cleared with at least 3 districts still lit. Defeat: immediate the instant a 3rd district goes fully dark.
- Each district has a 3-point light gauge; every incident that crosses its checkpoint without being destroyed removes 1 point.
- Towers: Rapid Relay (40cr / dmg 1 / range 2 / rate 0.5s → lvl1 range 3, rate 0.35s, 60cr → lvl2 dmg 2, rate 0.25s, 100cr), Zone Transformer (90cr / dmg 1 area / range 1.5 / rate 1s / faint field always visible, full radius on selection → lvl1 radius 2.0, 120cr → lvl2 radius 2.5, dmg 2, 180cr), Stabilizer (70cr / no direct damage / range 2.5 / slow 35% → lvl1 slow 50%, 90cr → lvl2 slow 65%, range 3, 140cr)
- Incidents: Surge (HP1, ×1.3 speed, 8cr), Traveling Short-Circuit (HP2, ×1.0, 12cr), Cascading Outage (HP3, ×0.7, 15cr), Sabotage (HP5, nights 4–5 only, 25cr). Per-night multipliers: HP ×(1+(night-1)×0.2), speed ×(1+(night-1)×0.1).
- Waves: spawn interval `max(0.8s, 1.2s-0.05×(night-1))`, incidents per wave `3+night+floor(wave/2)`.
- Score = incidents×10 + districts saved×100 + seconds remaining×2 + perfect waves×50 − districts lost×150, floored at 0. Best score in `localStorage`.

## Starting credits — corrected, verified by real math (150 credits)

An earlier draft of this project's documentation wrongly claimed a Zone Transformer plus a second tower was unaffordable at game start. **That was false and has been corrected.** The real affordable combinations at 150 starting credits are:

| Combination | Cost | Fits in 150? |
|---|---|---|
| Any single tower | 40 / 70 / 90 | Yes |
| Relay + Relay | 80 | Yes |
| Relay + Stabilizer | 110 | Yes |
| Relay + Zone Transformer | 130 | **Yes** (verified live in this session: built, 20cr left over) |
| Stabilizer + Stabilizer | 140 | Yes |
| Relay + Relay + Relay | 120 | Yes |
| Relay + Relay + Stabilizer | 150 | Yes (exact) |
| Zone Transformer + Stabilizer | 160 | No |
| Zone Transformer + Zone Transformer | 180 | No |

Only combinations whose total cost exceeds 150 are actually impossible. The code enforces exactly this (a purchase is refused only when `credits < cost`, nothing else) and was verified live: attempting a Relay after a Relay+Zone build (130 spent, 20cr left, Relay costs 40) was correctly refused for insufficient funds, while Relay+Zone itself (130 ≤ 150) was correctly allowed.

## A real bug found and fixed this session: Start button unreachable

While testing responsiveness, the animated background canvas on the start screen turned out to **not** behave as a true background. A CSS specificity conflict (`#screen-start > *` had the same specificity as `#bgcanvas` and came later in the stylesheet, so it silently overrode the canvas's `position:absolute` with `position:relative`) made the canvas a real flex-layout item that pushed the title, briefing, and **Start button below the visible viewport on every screen size** — the exact "Start button inaccessible" failure this brief warned against. Fixed by scoping the override to `#screen-start > *:not(#bgcanvas)`. Re-verified after the fix at 1600×900, 1024×480, 812×375 (phone landscape), 768×1024 (tablet), and 375×812 (phone portrait) — title, briefing, and Start button are all visible and reachable at every size, with natural page scroll available (not blocked) if a window is ever shorter than the content.

## Tests actually performed this session (not merely inferred from the code)

- **Responsive layout**, both files, screenshots taken and inspected at: large desktop (1600×900), short desktop (1024×480), tablet (768×1024), phone portrait (375×812), phone landscape (812×375). Start button visible and unclipped at every size; natural scroll confirmed reachable when content exceeds viewport height (tested on the in-game build bar at 812×375, scrolled to and used).
- **English version — full real victory**, all 25 waves, none skipped, each launched manually: final result "City Restored", score 3699 (this session) / earlier confirmed run also reached victory. Also a full real defeat (3 districts lost, no defense built) with correct score/stat readout.
- **French version — full real victory**, all 25 waves, played independently in this session (not just inferred from the English result): final result "Ville rétablie", score **4329**, 180 incidents neutralisés, **5/5 quartiers sauvés** (a flawless run), +829 time bonus, +1200 perfect-wave bonus, −0 penalty. Also a full real defeat in French, correct stats.
- **All 3 tower types** built and observed acting live (Relay's shot+impact trail, Zone Transformer's pulse and permanent faint field vs. full radius on selection, Stabilizer's animated rotating rings and slow effect).
- **Both upgrade levels** purchased live on a Relay (level 0→1→2), cost deducted correctly, upgrade burst effect visible, range halo shown on selection.
- **Invalid placement refused**: clicking a path tile (non-buildable) does nothing, no credits spent.
- **Insufficient-funds purchase refused**: attempting a 40cr Relay with 20cr remaining was correctly blocked, no tower placed, no credits deducted.
- **A valid, previously-mis-documented combination executed successfully**: Zone Transformer + Relay (130cr) built together in one game, 20cr left over exactly as the math predicts.
- **Wave progression**: manual launch required for every one of the 50 total waves played across both full playthroughs (25 EN + 25 FR) — none auto-started or auto-skipped.
- **District damage and district loss**: observed live (checkpoint hits, light-gauge decrease, a district going fully dark, life lost) in an earlier under-defended test run.
- **Night-transition screen**: seen and used at every night change (2, 3, 4, 5) in both languages.
- **Pause overlay**: triggered (accidentally, mid-wave) and confirmed showing "PAUSED" / "EN PAUSE" correctly, resumed correctly afterward.
- **Keyboard** (`1`/`2`/`3`/`Space`/`Escape`) and **mouse** interactions used throughout both full playthroughs.
- **Best score / `localStorage`**: write/read code path exercised (score displayed as "Best score" after each end screen); note under Limits below regarding sandbox persistence.

## Bugs found and fixed this session

1. **Start screen content pushed off-screen on every viewport size** (see dedicated section above) — root cause: CSS specificity conflict on the background canvas. Fixed and re-verified across 5 viewport sizes.
2. **False claim in prior documentation** that Zone Transformer + a second tower was unaffordable at 150 starting credits — corrected with an accurate combination table, verified by actually building that combination in the live game.
3. (Carried over from the previous session's fixes, reconfirmed still correct): HUD showing "Night 6" and stale controls during the victory animation; unrounded floating-point time bonus on the victory screen. Both still verified fixed.

## Tests NOT performed — stated honestly

- **Real touch input on a physical phone or tablet** was not tested — this environment cannot simulate genuine multi-touch hardware events. The code uses `pointerdown`/`pointermove` listeners, which unify mouse and touch in every modern browser, and the layout was verified to fit and remain legible at phone/tablet viewport sizes, but an actual finger-on-glass test is still outstanding and should be done by you before considering mobile support fully proven.
- **Clicking via simulated coordinates at the tablet preset (768×1024)** repeatedly failed to register in this specific sandboxed preview tool during testing, even though `elementFromPoint` confirmed the button was correctly on top and reachable, and a later retry (after switching away and back) succeeded normally. This looks like a transient input-timing quirk of the preview tool itself, not of the page — the same tablet layout was separately confirmed visually correct, and clicking worked normally at every other tested size (including phone preset and default desktop). Flagging this rather than papering over it.
- **A physical camera/screen recording of a session has not been made** — no video was created, per your explicit instruction; you will record the final video yourself once you've validated the game.
- Landscape phone gameplay was checked for layout and scroll-reachability of the controls, but a full wave was not played to completion in that specific orientation (only in portrait and desktop orientations was a full wave/night/victory played).

## Remaining limits

- Real `localStorage` persistence across a full close-and-reopen of a normal (non-sandboxed) browser was not independently re-verified this session — the read/write code itself is unchanged from the previous session (already wrapped in `try/catch` so a storage failure never breaks the end screen), and it did display "Best score" correctly after each win/loss this session, but a true cross-session persistence check on your own machine is worth doing once before final sign-off.
- No video, per instruction — that step is explicitly yours.

## Proofs available

Two independent full 25-wave victories were played and observed live in this session, one per language, with final scores, per-category breakdowns, and screenshots reviewed at the time (English: 3699 / earlier run; French: 4329, flawless 5/5). Responsive screenshots at 5 viewport sizes were reviewed for both the layout bug and its fix. All of this is described above with the exact numbers as they appeared on screen, not estimated or assumed.
