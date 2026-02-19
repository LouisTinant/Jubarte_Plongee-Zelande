# CLAUDE.md — Jubarte Plongée Zélande

This file describes the codebase for AI assistants working on this project.

---

## Project Overview

**Jubarte Plongée Zélande** is a single-page web application that provides real-time diving conditions for 33 scuba dive sites in the Dutch delta region (Zeeland, Netherlands). It is built for French-speaking divers of the **JUBARTE C.A.S. HABAY** club, authored by Louis TINANT (3-star diver).

The application aggregates live data from three public APIs and renders an interactive dashboard showing tidal windows, wind, wave conditions, underwater visibility estimates, and an embedded live weather map.

---

## Repository Structure

```
/
├── index.html      # Entire application (HTML + CSS + JS, 766 lines)
├── README.md       # Minimal title placeholder
└── CLAUDE.md       # This file
```

**There are no other files.** No build system, no package manager, no configuration files, no test suite.

---

## Technology Stack

| Layer | Choice |
|-------|--------|
| Language | Vanilla HTML5 / CSS3 / JavaScript (ES2020+) |
| Styling | Embedded `<style>` — CSS custom properties, Grid, Flexbox |
| Logic | Embedded `<script>` — async/await, Fetch API, Canvas API |
| Build | None — the file is deployed as-is |
| Dependencies | None — zero npm/CDN imports |
| Testing | None |
| CI/CD | None |

---

## Architecture: Single-File Application

Everything lives in `index.html`. The internal structure is:

```
index.html
├── <head>
│   └── <style>          Lines 7–170   — All CSS
├── <body>
│   ├── <header>         Lines 174–185 — Sticky nav with live indicator
│   ├── <main>           Lines 187–330 — All UI sections
│   │   ├── .sel-card                  — Site + date selectors
│   │   ├── #status-banner             — Dynamic dive status
│   │   ├── .card (tides)              — Kentering windows
│   │   ├── .grid2 (water + weather)   — 24h graph + wind compass
│   │   ├── .card (marine)             — Wave/swell data
│   │   ├── .card (visibility)         — Underwater vis estimate
│   │   ├── .card (windy)              — Embedded live map iframe
│   │   └── .sources                   — API health status
│   ├── <footer>         Lines 332–346 — Attribution
│   └── <script>         Lines 348–764 — All JavaScript
```

---

## Dive Sites Database (`SPOTS` object)

The `SPOTS` constant (lines 350–381) defines 33 sites across 4 zones:

| Zone | Sites | Tidal? | Notes |
|------|-------|--------|-------|
| **Oosterschelde** | 16 | Yes | Tidal estuary, kentering windows apply |
| **Grevelingenmeer** | 7 | No | Closed lake, dive anytime |
| **Veerse Meer** | 4 | No | Inner sea, dive anytime |
| **Westerschelde** | 3 | Yes | Expert only — strong currents, maritime traffic |

Each site object has these fields:
```js
{
  fr:      string,    // French display name
  zone:    string,    // Zone name
  rws:     string|null, // RWS station code (null = no tide data)
  corrHW:  number,    // High water correction in minutes vs reference station
  corrLW:  number,    // Low water correction in minutes
  tidal:   boolean,   // Whether site has tidal flow
  expert:  boolean,   // Experts-only warning
  noFlow:  boolean,   // True = no current, always diveable
  depth:   number,    // Max depth in metres
  vis:     [min, max],// Typical underwater visibility range (metres)
  lat:     number,    // WGS84 latitude
  lon:     number,    // WGS84 longitude
  desc:    string,    // French description (species, features)
}
```

**RWS stations** used: `STAVNS` (Stavenisse), `BRKDMDN` (Brouwersdam), `VLISSGN` (Vlissingen), `TERNZN` (Terneuzen).

---

## External API Integrations

### 1. Rijkswaterstaat (RWS) — Tide data
- **Endpoint:** `https://waterwebservices.rijkswaterstaat.nl/ONLINEWAARNEMINGENSERVICES_DBO/OphalenWaarnemingen`
- **Method:** POST with JSON body (Aquo+ format)
- **Auth:** None (public API)
- **Data:** Water height measurements in cm NAP (Amsterdam Ordinance Datum), every 10 minutes
- **Fallback:** If the API fails or returns empty, `fallbackTides()` generates synthetic tides using a 6.21-hour period, and `fallbackSeries()` interpolates heights between extremes using cosine smoothing.

### 2. Open-Meteo Standard — Weather
- **Endpoint:** `https://api.open-meteo.com/v1/forecast`
- **Method:** GET with query params
- **Auth:** None (public API)
- **Data:** Temperature, wind speed/direction/gusts, surface visibility
- **Model:** ECMWF, `Europe/Amsterdam` timezone, 2-day forecast, hourly resolution

### 3. Open-Meteo Marine — Waves
- **Endpoint:** `https://marine-api.open-meteo.com/v1/marine`
- **Method:** GET with query params
- **Auth:** None (public API)
- **Data:** Wave height/period/direction, swell parameters, wind wave height
- **Model:** ERA5, hourly resolution

### 4. Windy.com — Interactive map
- **Endpoint:** `https://embed.windy.com/embed2.html` (iframe embed)
- **Auth:** None
- **Data:** Live ECMWF wind/wave/temp overlay map centred on selected dive site

All fetches use `Promise.allSettled()` so a failing API never blocks the rest of the UI.

---

## Core JavaScript Functions

| Function | Lines | Purpose |
|----------|-------|---------|
| `fetchTide(rwsCode, date)` | 406–419 | POST to RWS API, return `{t, v}` series |
| `fetchWeather(lat, lon, date)` | 422–427 | GET Open-Meteo weather JSON |
| `fetchMarine(lat, lon, date)` | 430–435 | GET Open-Meteo marine JSON |
| `detectExtremes(series)` | 438–446 | Find HW/LW peaks in a time series using local max/min (±2 neighbours) |
| `fallbackTides(date)` | 447–456 | Synthetic extremes when API unavailable |
| `fallbackSeries(date, ex)` | 457–469 | Cosine-interpolated height curve between extremes |
| `drawGraph(series, corrected, dateStr)` | 472–519 | Canvas renderer: tide curve + dive window overlays + "now" marker |
| `renderTides(extremes, spot, dateStr)` | 522–559 | HTML: tide table with kentering windows; returns `corrected` array |
| `renderStats(series, spot, corrected)` | 562–577 | Current height, trend (Montant/Descendant), tidal range |
| `renderWeather(data, dateStr)` | 580–610 | Wind compass, Beaufort scale, temperature, gust alerts |
| `renderMarine(data, spot)` | 613–639 | Wave height/period/direction, swell, colour-coded severity |
| `renderVis(spot, weather, marine)` | 642–671 | Estimated underwater visibility; seasonal adjustments; site description |
| `updateStatus(corrected, spot, dateStr)` | 674–688 | Top status banner: green/yellow/red based on dive window timing |
| `renderTags(spot)` | 691–700 | Coloured tag pills under the site selector |
| `updateWindy(spot)` | 703–707 | Update Windy iframe `src` to selected site coordinates |
| `renderSources(tideOk, wxOk, marineOk)` | 710–722 | Data source list with online/offline badges |
| `loadAll()` | 726–759 | Main orchestrator: fetches all data in parallel, calls all renderers |
| `onSelChange()` | 761 | Event handler for site/date dropdowns |

---

## Key Constants

```js
SEA_TEMPS  = [5,5,7,9,12,15,18,19,17,14,11,7]  // Monthly water temps °C (Jan→Dec)
MONTHS_FR  = ['Jan','Fév','Mar','Avr',...]        // French month abbreviations
DIRS       = ["N","NNE","NE",...]                  // 16-point compass rose
```

---

## Business Logic: Kentering (Tidal Slack)

**Kentering** is the brief period around high or low tide when the current is minimal — the optimal dive window.

The algorithm:
1. Fetch the water height series for the day from RWS.
2. Detect HW and LW extremes with `detectExtremes()`.
3. Apply per-site tidal corrections (`corrHW`, `corrLW` in minutes) relative to the Stavenisse reference station. Corrections come from NOB (Nederlandse Onderwatersport Bond) tables cross-referenced with duikspotter.nl, sovscaldis.nl, and duikersgids.nl.
4. Each corrected extreme defines a **±30-minute dive window** centred on the kentering time.
5. The status banner and hero card reflect whether now is inside a window, approaching one, or between windows.

Sites with `noFlow: true` skip all tidal logic — they are always diveable.

---

## UI / CSS Conventions

- **CSS custom properties** defined on `:root` control all colours and spacing. Prefer using them over hardcoded values.
- Colour semantics:
  - `--green` / `--green-bg` → safe / diveable / online
  - `--yellow` / `--yellow-bg` → caution / approaching
  - `--red` / `--red-bg` → danger / no-dive / offline
  - `--accent` (#0071e3) → blue, informational / water / RWS data
- **Layout grids:** `.grid2` (2-col), `.grid3` (3-col), both collapse to 1-col on mobile.
- **Breakpoints:** 660px (grid collapse), 580px (selector grid), 480px (footer right hide).
- **Font:** System font stack (`-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Segoe UI'`).
- **Radius/shadow:** Use `var(--radius)` (18px) and `var(--shadow)` for cards.
- UI language is **French** throughout — all user-visible strings must be in French.

---

## Data Flow on Page Load

```
Page load / onSelChange()
  └── loadAll()
        ├── renderTags(spot)       — immediate (no fetch needed)
        ├── updateWindy(spot)      — immediate iframe src update
        ├── Promise.allSettled([
        │     fetchTide(),
        │     fetchWeather(),
        │     fetchMarine()
        │   ])
        └── on resolution:
              ├── detectExtremes() or fallbackTides()
              ├── renderTides()    → returns corrected[]
              ├── updateStatus()
              ├── renderStats()
              ├── renderWeather()
              ├── renderMarine()
              ├── renderVis()
              ├── drawGraph()
              └── renderSources()
```

Auto-refresh: `setInterval` every **600,000 ms (10 minutes)**, only when the selected date is today.

---

## Development Workflow

There is no build step. To develop:

1. Edit `index.html` directly.
2. Open it in a browser (or serve it with any static server, e.g. `python3 -m http.server`).
3. The browser's DevTools console is the primary debugging tool.
4. All API calls are cross-origin — ensure the browser does not block CORS (APIs are public and permissive).

To test locally without a server (file:// protocol), note that some browsers restrict Fetch from `file://` origins. Use a local server in that case.

---

## Adding a New Dive Site

1. Add an entry to the `SPOTS` object with all required fields.
2. Add a corresponding `<option>` in the `<select id="spot-select">` inside the correct `<optgroup>`.
3. If the site uses a new RWS station not in `RWS_MAP`, add it.
4. Tidal corrections (`corrHW`, `corrLW`) must be sourced from NOB tables or verified dive guides.
5. Set `noFlow: true` and `tidal: false` for still-water sites (lakes, sheltered bays).
6. Set `expert: true` for sites with strong currents or restricted access.

---

## Adding a New Zone

1. Create a new `<optgroup>` in the select element with an appropriate emoji and label.
2. Decide on an RWS station (or `null` for no-tide zones).
3. Update `renderTags()` zone→colour mapping if the zone name is new.
4. Update `renderMarine()` lake detection if the zone should suppress marine wave data.

---

## Modifying the Tide Graph (`drawGraph`)

The canvas renders at device pixel ratio (DPR) for sharp display on retina screens. Coordinate helpers:
- `nX(t)` → X pixel for a timestamp (linear over 24h)
- `nY(v)` → Y pixel for a height value (inverted, with 6%/78% margins)

Dive window overlays are rendered before the tide line so they appear underneath.

---

## Modifying the Visibility Algorithm (`renderVis`)

The base visibility (`uw`) is set by zone:
- Grevelingenmeer: 9m (clearest — closed lake)
- Veerse Meer: 7m
- Westerschelde: 2m (turbid river)
- Oosterschelde: 5m (default)

Seasonal modifiers:
- Spring (Mar–May): +2m (plankton bloom later, clearer)
- Summer (Jun–Aug): +1m
- Winter (Nov–Jan): −1m

Wave penalty: >0.8m significant wave height → −2m visibility.

Result is clamped to `[1, 15]` metres.

---

## Conventions for AI Assistants

- **Do not add a build system** unless explicitly requested. The zero-dependency nature is intentional.
- **Do not split into multiple files** unless explicitly requested. The single-file format enables trivial deployment.
- **All user-facing text must be in French** (no English in UI strings).
- **Preserve the CSS variable system** — do not hardcode colours.
- **API error handling** uses empty returns / null — do not throw uncaught exceptions. Follow the existing try/catch pattern.
- **No TypeScript, no transpilation** — stick to ES2020 features supported natively in modern browsers.
- **Canvas drawing** uses the DPR scaling pattern — preserve it when modifying `drawGraph`.
- **Tidal corrections are empirical data** — do not guess or invent values. Source from NOB or verified dive community data.
- When modifying `loadAll()`, maintain the `loading` flag guard to prevent concurrent fetches.
- The `corrected` array returned by `renderTides()` is the authoritative list of dive windows — other functions depend on it.

---

## Branch and Git Conventions

- Main branch: `master`
- Feature/AI branches: `claude/<description>-<session-id>`
- Commit messages should be descriptive and in English.
- Push with: `git push -u origin <branch-name>`
