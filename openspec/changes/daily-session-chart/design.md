# Design

## Overview

A second bar chart, `daily-session-chart`, sits directly beneath the existing rolling-7-day chart inside the single published file `web/index.html` (self-contained static HTML, no build step). It reuses the established visual language — the same bar rendering, goal styling, and card layout — parameterised from a new data array. No Google Sheet schema change.

## UX approval (gate — APPROVED, this is the locked design to implement)

UX gate passed. D K approved **Variant 1** on 15.11.26 from the interactive mockups `/opt/data/mockup_daily_chart_v3.html` (renders at `/opt/data/mockup_daily_chart_v3_box.png` = approved Box variant). The mockup shares the report's exact HTML/CSS, so what was approved is what ships — no design-to-code drift. Approved decisions, non-negotiable during implementation:

1. **Chart titles sit ABOVE their bars** — "Rolling 7-day total" and "Daily endurance minutes" each precede their chart.
2. **Linked 7-day window highlight = ONE rounded box, not per-bar outlines.** Selecting a rolling bar draws a single soft box over the 7 daily bars that comprise it; days outside the box are dimmed (`opacity ~.22`); the window's X-axis date labels darken/bold to match.
3. **Colour is consistent:** the selected rolling bar outline, the window box border, and any per-day emphasis are ALL the same near-black `#1f2421` — never green for one and black for another.
4. **Window alignment is exact:** the box's right edge ends at the *tapped* date's bar; its left edge at `[tapped − 6]`. The window end date equals the selected rolling bar's date and MUST include that date's own daily bar (e.g. selecting 14 Nov highlights 08–14, including 14 Nov).
5. **No pre-selection on load** — the box/highlight only appears after a bar is tapped.
6. **Tap-again toggles off** — tapping the already-selected bar deselects it and clears the box.
7. **left-edge clamp** — for the oldest bars (fewer than 7 days of history), the window clamps to the available days (e.g. taps on bar index 0–5 highlight 01–07 at most).

Date labels under the rolling chart remain grey `#a5aba5` unless a window is active; the highlighted window's daily labels use the dark/bold state. Rest days (0-min) show an empty bar slot inside the box, clearly part of the window.

## Data flow

`publish_report.py` already reads the `Zone 2 Volume` tab, which carries per-day `Total (min)` (column B) as a live formula summing that date's Endurance sessions. That is exactly what this chart needs — no new sheet read.

```
Training Records sheet
  Endurance tab (per-session rows) ──(live SUMIFS formula)──► Zone 2 Volume tab
                                                              col B = per-day total min
                                                              col A = date
        ▲                                                              │
        │ publish_report.py (existing read of Zone 2 Volume A:D)      │
        └──────────────────────────── new DAILY[] array ─────────────┘
   ──────────────────────────────────────────► into template → web/index.html
```

- `daily-session-chart`'s bar heights come from **`DAILY[]`** = the same date–value pairs as `dates[]`/`rolling[]` but with column **B (daily total)** instead of column **D (7-day rolling)**. Since both share the same 14 latest dates and same sheet read, the two charts stay aligned in time and never disagree on dates.
- The existing `dates` array (14 entries) is reused as the X labels; a new `daily` array holds the per-day minute totals for those same dates.
- Both publish triggers (on-log and the 23:01 UTC daily reconcile) already keep `Zone 2 Volume` current, so `DAILY[]` refreshes through the normal pipeline — no new cron.

## Template changes (`hung_endurance_report.html`)

Add a new data array next to the existing ones (top of script):

```js
const daily = [ 0, 45, 0, 60, 30, 0, 90, 0, ... ];   // minutes per date, aligned to dates[]
```

Beneath the existing rolling chart card, render a second card titled **"Daily endurance minutes"** (title ABOVE the bars), with the same bar-drawing logic parameterised over `daily`:

- One bar per date; bar height proportional to `daily[i] / maxDaily`.
- A date with `0` renders a zero-height bar (empty slot on the axis), preserving alignment with the rolling chart above.
- Same European `DD.MM` date labels already used; distinct title above the chart.
- Empty state: if `daily` is empty/all-zero, show "No endurance sessions yet." rather than a blank area.

**Reuse, don't fork the bar CSS**: factor the existing bar chart markup/class so both charts share one render path (parameterised by array + max), rather than duplicating the block. This preserves the settled design and keeps future edits to one place.

## Selection logic (linked 7-day window) — the approved state machine

The rolling-7-day bars gain a `click` handler that ties the top chart to the daily chart below. Implement exactly this:

- **State:** `sel` = index into `dates[]`/`rolling[]`, or `-1` (none). Default on load: `sel = -1` — nothing highlighted, no box, no dim.
- **Render when `sel` is set** (`sel >= 0`):
  - The rolling bar at `sel` gets the `.sel` class (black `#1f2421` outline).
  - A rounded box (`#1f2421` border, light black fill) is drawn over the daily bars `[max(0, sel-6) .. sel]` — i.e. exactly the 7-day window that produced that rolling total, ending at the tapped bar's date.
  - Daily bars outside `[max(0, sel-6) .. sel]` get the `.hdim` dim class; window bars keep full colour.
  - Daily X-axis labels in the window get the `.hl` dark/bold state.
  - Box geometry measured in **px from `offsetLeft/clientWidth`** of the pinned day bars (the same "resolve both rulers against one box" rule from the rolling goal-line) so it can never drift off the bars.
- **Tap a different rolling bar** → `sel` updates, box + dim + labels re-anchor to the new window (smooth `transition` on box position).
- **Tap the already-selected bar again** → `sel = -1`, all highlights cleared (toggle off).
- **Tap anywhere outside a rolling bar** → `sel = -1`, cleared (same "tap outside clears" rule the existing tooltip uses).
- **Left-edge clamp:** `lo = max(0, sel-6)` guarantees the oldest bars (fewer than 7 in-range days) clamp to the earliest available date; the box never extends before the chart's start.

The daily chart's own bars are tappable via the SAME shared tooltip function as the rolling bars (`enableBarTooltip(boxId, values)`) — one function, called once per chart with its own value array, so a daily tap shows a black outline + that day's total, identically to the rolling chart. The daily bars are NOT part of the 7-day window *selection* (no per-day outline mode on the window; that rejected Variant 2 stays out).

The window box must not cover the bars and must frame them evenly: its left/right edges are placed **one full flex-gap outside** the first/last enclosed bars (`left = first.offsetLeft − gap`; `right = cw − lastEl.offsetLeft − lastEl.offsetWidth − gap`, `gap = flex gap = 5px`), and `#barsD` carries `padding-bottom:5px` to match. Veried result: the frame sits an equal ≈5px from the bars on the left, right, and bottom (D K asked for consistent spacing on all sides, not 14px on the bottom).

## publish_report.py changes

In `main()`, extend the existing `Zone 2 Volume` read to also capture column B (daily total) alongside column D (7-day rolling) for the same 14 picked dates, and inject `const daily = [...]` into the template via the same `re.sub` pattern already used for `rolling`. Add `daily` to the `sig` JSON so an unchanged daily series stays un-published (no spurious commits).

The EVALS read and re-sub are untouched.

## Out of scope

- No new sheet columns, tabs, or cron jobs (per-day totals already exist in `Zone 2 Volume` col B).
- The rolling 7-day chart's tooltip and value-drawing are unchanged; selection ADDS a highlight but does not alter bar heights or the existing tooltip.
- No pre-selection of the current week on load (approved: nothing selected until a bar is tapped).
- No per-day outline mode for the 7-day window (Variant 2 was considered and rejected — Variant 1 box is the approved look). Daily bars ARE tappable, but only for the shared value-tooltip, not for the window selection.
- No auto-annotation of which session type made a day's bar (sum of all types, per the spec).