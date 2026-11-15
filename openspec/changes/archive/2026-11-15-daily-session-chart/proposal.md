# Proposal

## Why

Hung's report shows D K's endurance as the rolling 7-day total only. That single number tells her whether she hit the 180-min week but hides the daily shape behind it: whether a good week came from steady daily sessions or one big day, how many active days she had, and how this week's daily effort compares to last. She wants to **see each logged endurance session's duration on its date** — a per-day bar chart under the rolling total — so the "did I hit it" number is backed by an at-a-glance view of *how the minutes were made up day by day.*

## What Changes

- **New capability `daily-session-chart`**: add a second bar chart beneath the rolling-7-day chart. X axis = date (day), Y axis = minutes of endurance logged that day. One bar per day = the **sum of all endurance sessions on that date** (any type — walking, treadmill, cycle — since everything endurance is logged to the same sheet). Rest/empty days show no bar (0 min).

## Capabilities

- **New**: `daily-session-chart`

## Impact

- Extends `web/index.html` (the single published file) — adds one data array, a second chart, and the linked 7-day window selection (tap a rolling bar to highlight its 7 days below, approved UX Variant 1).
- Extends `publish_report.py`: reads the per-day `Total (min)` column (B) of the existing `Zone 2 Volume` tab — already computed as a live formula — and injects it as a new `DAILY[]` array. No new sheet read, no schema change.
- The daily-reconcile and on-log publish triggers already rebuild the `Zone 2 Volume` tab, so the new chart stays current with zero extra scheduling.
- **BREAKING**: none.