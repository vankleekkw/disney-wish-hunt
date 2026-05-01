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
- **Dynamic color system:** Player colors applied via inline styles — no hardcoded CSS class names like `.kris`.
- **Setup persistence:** Group name + player config stored in `wish_setup_v1`. Game state in `wish_state_v1`. Bonus finds in `wish_bonus_v1`.

## Structure
```
index.html
├── CONFIG block       — SHIP_DATE, CREDIT_LINE, PALETTE, storage keys (top of <script>)
├── SHIP_LOCS          — Bonus find location dropdown options
├── FAMILY_SECS        — Egg data: family-friendly areas (ship)
├── NIGHT_SECS         — Egg data: nightclubs / adult venues
├── ISLAND_SECS        — Egg data: Castaway Cay on-land finds only (no facts)
├── Setup screen       — First-launch overlay; selectCount(), renderSetupRows(), commitSetup()
├── Game render        — buildSections(), setButtonState(), updateStrip()
├── Bonus modal        — openBonusModal(), saveBonus(), renderBonusCards()
├── Scorecard          — buildScorecard() — built on tab switch
├── Pass Along tab     — buildPassAlong() — QR code + standings + credit line
└── Export/Import      — exportData(), importData()
sw.js                  — Cache key: wish-hunt-v2
```

## Tabs
1. 🏰 Family — ship-wide findable items, all ages
2. 🌙 Nightclubs — adult venue finds (no age labels in UI)
3. 🏝️ Island — Castaway Cay physical finds only
4. 🏆 Scores — full scorecard, export/import, reset
5. 📲 Share — QR code + live standings + credit line (doubles as door display)

## Credit Line
`Courtesy of the VanKleek Family · Stateroom 8570 · Elmhurst, IL`  
Defined as `CREDIT_LINE` constant — update here for future cruises.

## Ship Date
`Jun 1–5, 2026` — defined as `SHIP_DATE` constant at top of script.

## Deployment
GitHub Pages from `main` branch root. Push to `main` → auto-deploys in ~60s.  
Service worker cache key is `wish-hunt-v2` — bump to `v3`, `v4` etc. when making breaking changes to force cache refresh on users' devices.

## Common Future Edits
- **Add/remove egg items:** Edit the relevant `*_SECS` array
- **Update dates or stateroom:** Change `SHIP_DATE` and `CREDIT_LINE` constants
- **New cruise ship:** Update location data in `SHIP_LOCS` and all three `*_SECS` arrays
- **Bump cache:** Increment cache key in `sw.js` after significant changes
