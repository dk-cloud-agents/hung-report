# Proposal

## Why

The report shows total endurance volume, but D K's training follows Peter Attia's 80/20 guideline where the **strict Zone 2** base is the meaningful metric (the zone that builds aerobic base). Total volume hides how much of it was actually Zone 2 — a week can hit the 180-minute goal yet contain little true Z2 work. She wants to flip the volume view to strict Zone 2 minutes to judge whether the week followed Attia's guidelines and will be effective.

## What Changes

- **New capability `zone-2-filter`**: a UI filter control (apply/remove) that switches the report's volume display between total endurance minutes and strict Zone 2 minutes.
- When the filter is ON, **three surfaces flip together**: the headline number ("X min / 180 goal"), the 7-day rolling chart, and the daily endurance-minutes chart. When OFF (default), today's behavior — total endurance everywhere.
- The choice is **not remembered**: every page load starts at OFF (total endurance view).
- Data comes from strict Zone 2 columns the sheet **already computes** (per-day `Zone 2 (min)` col C; `7-day Zone 2 Total (min)` col E; live formulas on the HR 107–130 band) — no sheet schema change, pure template + `publish_report.py` change.
- The treadmill eval tracker is unchanged.
- **BREAKING** none.

## Capabilities

### New Capabilities
- `zone-2-filter`: switching the report's volume surfaces (headline, 7-day rolling chart, daily chart) between total endurance minutes and strict Zone 2 minutes via an on/off UI filter.

### Modified Capabilities
(none)

## Impact

- Extends `web/index.html` (the single published file) and `publish_report.py` (reads 2 more sheet columns, injects 2 more data arrays).
- Data source: existing `Zone 2 Volume` tab columns C and E — verified live 23.11.26 (live formulas, no schema change).
- UX approval gate applies (user-facing control): interactive mockup `mockups/zone2-filter-mockup.html` — D K approves it before implementation.
- JIRA tracking: **SCRUM-8** (Story, label `hungs-fitness-report`), flow per SCRUM-7 workflow.
