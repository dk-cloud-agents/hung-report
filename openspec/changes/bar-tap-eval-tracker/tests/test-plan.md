# Test Plan — bar-tap + eval-tracker + collapse header

> **APPROVAL NEEDED BEFORE IMPLEMENTATION.** Reviewed by D K; implementation starts only on her approval.
> After implementation every case is filled with REAL results (pass/fail, evidence, timestamp). NA + reason where not runnable. Results are never fabricated.

## bar-detail (tap → number tooltip)

| # | Test case | Input | Expected outcome | Status |
|---|-----------|-------|------------------|--------|
| T1 | Tap newest bar | Tap rightmost bar | Tooltip above it shows only that date's trailing-7-day total number | pending |
| T2 | Tap a past bar | Tap an older bar (e.g. 11-06) | Tooltip shows that past date's total (recoverable) | pending |
| T3 | Tooltip = bar's value | Tap bar at value T | Tooltip shows exactly T, sourced from `rolling[]`, nothing else | pending |
| T4 | Tap outside bars | Tap chart area but not a bar | Open tooltip closes / no change | pending |

## Collapse header (Slim)

| # | Test case | Input | Expected outcome | Status |
|---|-----------|-------|------------------|--------|
| T5 | Collapse on scroll | Scroll the window and, separately, the inner container (both are the real scroller on different layouts) | Header becomes a single line (avatar left, goal right), tagline hidden | pending |
| T6 | Expand at top | Scroll back to top | Header returns to full avatar + number + tagline | pending |

## eval-tracker (verdict table)

| # | Test case | Input | Expected outcome | Status |
|---|-----------|-------|------------------|--------|
| T7 | Table renders | Page with EVALS populated | Table "Treadmill evals · 15% incline / 30 min" shows Date/Efficiency/Work/Status cols newest-first | pending |
| T8 | Status correctness | HR up, speed same (30.10 vs 29.10) | Status = "Regression" (red) | pending |
| T9 | Improvement correct | Speed up, HR same/down | Status = "Improved" (green) | pending |
| T10 | First eval | First/oldest row | Status = "start" (grey) | pending |
| T11 | Efficiency glyph colour | HR down vs prev | Efficiency cell shows green ✓ | pending |
| T12 | Work glyph colour | Speed up vs prev | Work cell shows green ✓ | pending |
| T13 | Regression glyph colour | HR up at same speed | Efficiency shows red ✗ | pending |
| T14 | Empty state | Page with no EVALS | Table shows empty message, no error | pending |
| T15 | Long-session eligibility | A >30-min eval without 30-min-window HR in data | Excluded from table (documented) | pending |
| T16 | Console clean | Page loads | 0 console errors | pending |

## Execution rule

- T1–T16 run in a real browser against the deployed `web/index.html` (tap/scroll simulation).
- Every result carries evidence (DOM state / console output) + timestamp.
- T15 is a data-eligibility check: verify no unqualified longer session appears in the table.