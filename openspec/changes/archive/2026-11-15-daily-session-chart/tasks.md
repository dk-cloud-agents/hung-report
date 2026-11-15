# Tasks

## UX approval (gate — PASSED 15.11.26)

- [x] Build the interactive mockup `/opt/data/mockup_daily_chart_v3.html` + renders (`_v3_box.png` approved).
- [x] D K approved **Variant 1** (single black box). Decisions recorded in design.md: titles above charts, one black box (not per-day outlines), consistent `#1f2421` black everywhere, window ends on the tapped date, no pre-selection on load, tap-again toggles off, left-edge clamp.

## Feature — daily-session-chart

- [x] Add the `daily = [...]` data array to the template (`hung_endurance_report.html`), aligned to the existing `dates[]` (same 14 slots).
- [x] Render a second bar chart card beneath the rolling-7-day chart, **title ABOVE the bars** ("Daily endurance minutes"), reusing the existing bar CSS/classes: one bar per date, height ∝ `daily[i] / maxDaily`; 0-min dates show an empty slot (bar retained for axis alignment).
- [x] Reuse the report's European `DD.MM` date labels + minutes axis.
- [x] Sum-of-the-day semantics: bar height = total minutes for that date across all endurance types (sourced from the sheet's per-day total — no per-type splitting).
- [x] Add an empty state: "No endurance sessions yet." when `daily` is empty or all-zero.

## Feature — linked 7-day window selection

- [x] Add a `click` handler to each rolling-7-day `.bar` with a `sel` index state (default `-1` = nothing selected).
- [x] On select: outline the tapped rolling bar in black and draw a **single rounded box** (`#1f2421` border + light fill) over daily bars `[max(0, sel-6) .. sel]`; dim daily bars outside via `.hdim`; darken/bold the window's daily date labels (`.hl`).
- [x] Measure the box in px from the pinned day bars' `offsetLeft/clientWidth` (both rulers resolved against one box — no % mixing), so it aligns exactly.
- [x] Tap a different rolling bar → re-anchor box+dim+labels to the new window (smooth transition).
- [x] Tap the already-selected bar again (or tap outside the rolling bars) → `sel = -1`, clear all highlights.
- [x] Left-edge clamp: `lo = max(0, sel-6)` so oldest bars never extend the box before the chart's start.
- [x] Daily bars use the shared `enableBarTooltip('barsD', daily)` — tap → black outline + that day's total, same function as rolling.
- [x] Weekbox frames the bars an equal ≈5px on left/right/bottom (inset by full flex gap on the sides + `#barsD{padding-bottom:5px}`), never over the bars.
- [x] Per-day outline mode for the window is NOT implemented (rejected Variant 2).

## publish_report.py

- [x] Extend the existing `Zone 2 Volume` read to capture column B (per-day `Total`) alongside column D for the same 14 picked dates.
- [x] Inject `const daily = [...]` via the same `re.sub` pattern used for `rolling`.
- [x] Include `daily` in the `sig` JSON so an unchanged daily series does not re-publish.
- [x] Re-run on-log: after a session is synced + published, the new chart reflects the new day's minutes.

## Test plan (approve first, then execute)

- [x] Draft `tests/test-plan.md` mapping each REQ/AC to a concrete case (sample `daily` arrays: normal, a multi-session day summed to one bar, an all-zero stretch, empty array).
- [x] Execute against the real page in a browser; fill `tests/results.md` with real pass/fail + evidence (DOM assertions, console-error capture per the dashboard skill).
- [x] Run `openspec validate` on the change until clean.

## Ship

- [x] Update the template + `web/index.html`; verify the page renders in a browser, then push (scoped `git add index.html` for the report; plan files in their own commit).
- [x] Present results to D K; on approval `openspec archive`.
- [x] Update the fitness skill's visual-dashboard reference to note the new daily chart + data array.