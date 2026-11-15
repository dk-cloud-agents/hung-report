# Test Results — daily-session-chart

Executed: 2026-11-15, against `web/index.html` (the deployed file) via `browser_exec` — real `click`
events dispatched, DOM state asserted, console errors captured with the navigation-surviving CDP shim.
Real sheet data (14 dates: 11-02..11-15; `daily` from Zone 2 Volume col B).

| # | Test | Result | Evidence |
|---|---|---|---|
| 1 | Daily chart below rolling chart; titles above | **PASS** | 14 `#barsD .bar`; titles = `['Rolling 7-day total','Daily endurance minutes']` in order |
| 2 | Daily array = sheet per-day totals | **PASS** | `daily` = 14 values, `[53, 24, 35, 38, 0, 54, 30, 28, 31, 0, 44.5, 39.2, 62, 0]`, aligned 1:1 with `dates` |
| 3 | Rest days show empty slot (axis preserved) | **PASS** | zero-value dates (idx 4, 9, 13) render height `0px`; 14 slots retained |
| 4 | Empty state (no sessions) | **PASS** | `daily=[]` temp copy shows "No endurance sessions yet"; daily labels cleared; no console error |
| 5 | Tap rolling bar → highlight window | **PASS** | Tap idx13: `.sel` on 1 bar; `.weekbox` visible; 7 daily bars dimmed |
| 6 | Window ends on/includes tapped date + sums | **PASS** | Tap 15 Nov → labels `11-09..11-15` (window ends at tapped date); window sum 204.7 = rolling[13] 204 (± int rounding) |
| 7 | No pre-selection on load | **PASS** | Fresh load: 0 `.sel`, `.weekbox` display `none` |
| 8 | Tap different bar re-anchors | **PASS** | idx13→idx5: box `block`, highlighted labels move to `11-02..11-07` |
| 9 | Tap selected bar again → deselect | **PASS** | 2nd tap on selected bar: 0 `.sel`, box `none`, 0 dimmed |
| 10 | Tap outside → clear | **PASS** | After selecting, `body` click → 0 `.sel`, box hidden |
| 11 | Consistent black emphasis | **PASS** | `.sel` outlineColor == `.weekbox` borderColor == `rgb(31, 36, 33)` (#1f2421) |
| 12 | Left-edge clamp (oldest bar) | **PASS** | Tap idx0 → highlights only `11-02` (1 available day); never before chart start |
| 13 | No regression (rolling/tooltip/evals) | **PASS** | Rolling bars, tap-to-reveal tooltip, eval table (7 rows) unchanged |
| 14 | No console errors across all interactions | **PASS** | `window.__con` empty on load and after every case |
| 15 | Daily bars tappable (shared function) | **PASS** | Tap daily idx12 → 1 black `.tapped` outline, tooltip "62" (= `daily[12]`); tap-again/outside clears; `enableBarTooltip` single code path for both charts |
| 16 | Window box inset (border beside bars) | **PASS** | Select rolling idx13: `boxLeft = first.offsetLeft − 2.5px`, `boxRightEdge = last.offsetLeft + width + 2.5px` (half-gap = 2.5), border in the gap, not over bars |

## N/A (not runnable here, stated reason)
- iOS Safari touch/scroll **feel** and on-device layout — cannot verify headlessly. Flagged for D K's own
  on-phone check at the user-acceptance gate (deployed live at https://dk-cloud-agents.github.io/hung-report/).