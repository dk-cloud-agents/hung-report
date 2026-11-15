# Test Plan — daily-session-chart

Scope: the new "Daily endurance minutes" chart + the linked 7-day selection (approved UX Variant 1).
Method: drive the actual page with `browser_exec` — dispatch real `click` events, assert on resulting DOM
state (classes, box geometry, label states, counts), and capture console errors via the navigation-surviving
CDP shim. Real data from the Training Records sheet is used where possible; controlled sample arrays are
substituted (temp copy) for edge states.

## REQ/AC → Test cases

| # | Requirement / AC | Input | Expected outcome | Status |
|---|---|---|---|---|
| 1 | Daily chart renders beneath rolling chart | Page loads with `daily` + `rolling` arrays | A second chart titled "Daily endurance minutes" exists BELOW the rolling chart; both titles sit above their bars; 15 bars match `daily` | pending |
| 2 | Bar height = that date's total minutes (all endurance types summed) | `daily` contains a date with multiple summed sessions | That date renders one bar whose height reflects the combined minutes (matches the sheet's per-day total) | pending |
| 3 | Rest days show an empty slot (axis preserved) | `daily` has 0-min dates | Those dates render a zero-height/empty bar but keep their date slot, aligning with the rolling chart | pending |
| 4 | Empty state | Temp copy with `daily = []` | Chart area shows "No endurance sessions yet." — no blank/broken render, no console error | pending |
| 5 | Tap rolling bar → highlights its 7-day window | `click` bar at index 13 (14 Nov) | Selected bar has `.sel` (black outline); a single box (`.weekbox`, `#1f2421` border) encloses daily bars 08–14; days outside dimmed (`.hdim` count = 8) | pending |
| 6 | Window ends on and includes the tapped date | Select 14 Nov (idx 13) | Box right edge sits on the 14 Nov daily bar; highlighted labels are 08,09,10,11,12,13,14 (14 included); sum of window daily values = rolling[13] | pending |
| 7 | No pre-selection on load | Fresh load, no interaction | `sel` = none; no `.sel` on any bar; box hidden; nothing dimmed | pending |
| 8 | Tap a different rolling bar re-anchors | Select idx 13, then idx 5 | Box + dim + labels move to the new window (box px position changes; hdim count updates) | pending |
| 9 | Tap selected bar again → deselect | Tap idx 13 twice | On 2nd tap: `sel` cleared, box hidden, no `.dim`, no `[.sel]`, no highlighted labels | pending |
| 10 | Tap outside bar → deselect | Select then `document` click not on a bar | Selection cleared like #9 | pending |
| 11 | Consistent black emphasis | Any selection active | Selected bar outline, box border, and label highlight all resolve to `#1f2421` (no green in the highlight) | pending |
| 12 | Left-edge clamp | Select idx 0 (oldest bar) | Box covers available days from chart start only (0..6 → all 7, or clamps before start); never extends before day 01 | pending |
| 13 | Rolling/EVALS unchanged | Compare chart/tooltip/EVAL markup | Rolling chart values, tap-to-reveal tooltip, and eval table are unchanged by this feature | pending |
| 14 | No console errors | Every step above | `window.__con` is empty on load and after each interaction | pending |
| 15 | Daily bars tappable (shared function) | Tap a daily bar (idx12, `daily=62`) | Bar shows black `.tapped` outline + tooltip "62" — same behaviour/logic as rolling bars | pending |
| 16 | Window box inset (border beside bars) | Select a rolling bar (idx13) | Box left/right edges sit one flex-half-gap outside the first/last enclosed bars, not over them | pending |

## Not runnable / N/A (stated reason)

- Cross-device iOS Safari *feel* (scroll, touch) cannot be verified headlessly — flagged for D K's own on-phone
  user-acceptance check after deployment (per SDD two-stage gate).