# Design

## Overview

Both features live inside the single published file `web/index.html` (a self-contained static page; no build step). Data stays in JS arrays — the page is generated from `hung_endurance_report.html` (the template) by `publish_report.py`. No schema change to the Google Sheet.

## Architecture

```
web/index.html
  ├─ script: bars (existing) + tap handler        → bar-detail
  ├─ script: eval list (new, below chart)          → eval-tracker
  └─ data arrays: dates[], rolling[] (existing),
       plus a new EVALS[] array (date, hr, speed)
```

## Feature 1 — bar-detail

- Each `.bar` div gets a `click` handler. On click, read the bar's date from the `dates[]` and its value from `rolling[]` (same index — already aligned).
- Render a readout (an overlay or a line above the chart): `"<date>: 254 min in the last 7 days"`.
- Tap outside bars (`document` click where `event.target` isn't a bar) clears the readout.
- No new data source — sourced from the existing `rolling[]` so the number always matches the drawn bar height.

## Feature 2 — eval-tracker

- Add a data array near the top of the script:
  ```js
  const EVALS = [
    { d: "05.08", hr: 113, kmh: 2.4 },
    { d: "10.08", hr: 106, kmh: 2.5 },
    { d: "18.08", hr: 112, kmh: 3.0 },
    { d: "29.10", hr: 136, kmh: 3.3 },
    { d: "30.10", hr: 138, kmh: 3.3 },
  ];
  ```
- Under the chart, render a titled section "Treadmill evals · 15% / 30 min". Each row: `date · <hr> bpm @ <kmh> km/h`.
- Trend tags: for each row after the first, compare this row's HR and speed to the previous. HR: ↓ if this < prev −2, ↑ if > prev +2, else →. Speed: ↑ if > prev +0.05, ↓ if < prev −0.05, else →. Separate tags: e.g. `HR ↓ · speed ↑`.
- Empty state: if `EVALS` is empty, show "No evals yet."
- Data is edited by hand in the template when a new eval is logged; the publish script does NOT auto-derive it (eval data is small, curated, and requires the 30-min-window judgment). Extending `publish_report.py` to add a per-eval row is out of scope for this change unless D K asks.

## Out of scope

- Auto-ingesting eval rows from the sheet (the 30-min-window HR requires human judgment about the window).
- Styling overhaul; the readout and eval list reuse the existing visual language.