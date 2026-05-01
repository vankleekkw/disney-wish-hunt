# Disney Wish Easter Egg Hunt

**Live URL:** https://vankleekkw.github.io/disney-wish-hunt/  
**Repo:** https://github.com/vankleekkw/disney-wish-hunt  
**Stack:** Single-file HTML + PWA service worker (sw.js) — no build step, no dependencies

## Purpose
A mobile-first Easter egg hunt game for Disney Wish cruise guests. Designed to be shared via QR code so any group on the ship can play independently with their own scores.

## Key Design Decisions
- **Self-contained:** All CSS, JS, and egg data are in `index.html`. `sw.js` handles PWA/offline caching.
- **No backend:** State lives entirely in `localStorage` per device. Groups are isolated.
- **Configurable players:** 1–8 players set up on first launch (name + emoji + color). No hardcoded names.
- **Dynamic color system:** Player colors applied via inline styles — no hardcoded CSS class names.
- **Setup persistence:** Group name + player config stored in `wish_setup_v1`. Game state in `wish_state_v1`. Bonus finds in `wish_bonus_v1`.
- **Nightclubs excluded from main score:** `playerTotal()` only counts `FAMILY_SECS` + `ISLAND_SECS`. Nightclub totals appear as "Parent Bonus" in scorecard and Share tab.

## Structure
```
index.html
├── CONFIG block       — SHIP_DATE, CREDIT_LINE, PALETTE, PALETTE_DARK, storage keys (top of <script>)
├── SHIP_LOCS          — Bonus find location dropdown options
├── FAMILY_SECS        — Egg data: all-ages areas (ship + kids club + restaurants)
│   ├── outside        — Exterior: Rapunzel stern, Captain Minnie bow, hull scroll Mickeys
│   ├── grandhall      — Deck 3: Cinderella chandelier, glass slipper, woodland creatures
│   ├── wonderland     — Deck 3: Wonderland Cinema doorknobs, Cheshire Cat, card motifs
│   ├── near1923       — Deck 3: Beanstalk mural, Wish cameo, stairwell art
│   ├── restaurant1923 — Deck 3: Hidden Mickey in name, 1000+ animation artifacts
│   ├── wishing        — Deck 4: Jiminy Cricket, Pinocchio mosaics
│   ├── stairs         — Stairwells & corridors: fleet artwork, transformation panels
│   ├── kids_oceaneer  — Deck 5: Oceaneer Club — Star Wars porgs, Avengers, Alice, Frozen
│   ├── arendelle      — Deck 11: Olaf carvings, snowflakes, troll eyes, Northern Lights Mickey
│   ├── worldsofmarvel — Deck 11: Arc Reactor ceiling, Pym Tech objects, Stark-itecture
│   ├── marceline      — Marceline Market: train motifs, Walt's hometown stall names
│   └── throughout     — Bibbidi Bobbidi, stateroom carpets, lamp Mickey, Tangled sun icons
├── NIGHT_SECS         — Egg data: nightclubs / adult venues (excluded from main score)
│   ├── nightingales   — Deck 3: bubble chandelier, sheet music
│   ├── bayou          — Deck 3: Tiana's bar, firefly ceiling, alligator nods
│   ├── hyperspace     — Deck 3: 5 planet viewports, Falcon, ships, holotube
│   ├── luna           — Decks 4–5: 4 hidden Mickey locations + karaoke Easter eggs
│   ├── keg_pub        — Deck 5: Nautilus, 4 octopuses, Marc Davis painting, Imagineers
│   ├── keg_map        — Deck 5: ceiling map, compass coordinates, shipyard coords
│   ├── hooks          — Deck 12: Hook's Barbery + secret bar, Neverland map
│   ├── therose        — Deck 12: enchanted rose, magic mirror, Cogsworth gears
│   ├── enchante       — Deck 12: Lumiere, Mrs. Potts & Chip, Cogsworth
│   ├── palo           — Deck 12: Italian wine art, DCL anchor tile, ceiling medallion Mickey
│   └── quietcove      — Deck 13: Quiet Cove + spa awning Mickey from observation deck
├── ISLAND_SECS        — Egg data: Castaway Cay dry-land finds only
│   ├── cc_mickeys     — Heads Up Bar coins, Cookie's Too shelf, Observation Tower fan
│   └── cc_landmarks   — Mount Rustmore, Pelican Plunge, Olaf's Freeze, post office, Spring-A-Leak
├── SHIP_FACTS         — Fun facts shown on Share tab ("Did You Know?") — not scorable
├── Setup screen       — First-launch overlay; selectCount(), renderSetupRows(), commitSetup()
├── Game render        — buildSections(), setButtonState(), updateStrip()
├── Bonus modal        — openBonusModal(), saveBonus(), renderBonusCards()
├── Scorecard          — buildScorecard() — built on tab switch
├── Pass Along tab     — buildPassAlong() — QR code + standings + credit line + Did You Know
└── Export/Import      — exportData(), importData()
sw.js                  — Cache key: wish-hunt-v16
```

## Tabs
1. 🏰 Family — ship-wide findable items, all ages (scored)
2. 🌙 Nightclubs — adult venue finds; Parent Bonus section in scorecard (not in main total)
3. 🏝️ Island — Castaway Cay physical finds only (scored)
4. 🏆 Scores — full scorecard, export/import, reset
5. 📲 Share — QR code + live standings + nightclub bonus + Did You Know facts

## Color System
- `PALETTE` — bright colors for dark mode (default)
- `PALETTE_DARK` — same hues, deeper/darker for light mode text and borders
- `themeColor(hex)` — maps PALETTE → PALETTE_DARK via `_pmap` at render time
- `sectionColor(hex)` — lightens in dark mode, returns as-is in light mode
- All player color assignments route through `themeColor()` — never hardcode

## Credit Line
`Courtesy of the VanKleek Family · Stateroom 8570 · Elmhurst, IL`  
Defined as `CREDIT_LINE` constant — update here for future cruises.

## Ship Date
`Jun 1–5, 2026` — defined as `SHIP_DATE` constant at top of script.

## Deployment
GitHub Pages from `main` branch root. Push to `main` → auto-deploys in ~60s.  
Service worker cache key is currently `wish-hunt-v16` — bump the number in `sw.js` after any significant content change to force cache refresh on users' devices.

**iPhone PWA cache clearing:** removing the app from Home Screen and re-adding is the reliable method. Safari → Settings → Privacy → Manage Website Data → remove `github.io` entry also works.

## Common Future Edits
- **Add/remove egg items:** Edit the relevant `*_SECS` array — scoring and scorecard update automatically
- **Add a fun fact:** Append to `SHIP_FACTS` array — appears on Share tab only, not scored
- **Update dates or stateroom:** Change `SHIP_DATE` and `CREDIT_LINE` constants
- **New cruise ship:** Update `SHIP_LOCS`, all three `*_SECS` arrays, and `SHIP_FACTS`
- **Bump cache:** Increment cache key in `sw.js` after any significant change
