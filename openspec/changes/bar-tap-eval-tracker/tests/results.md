# Test results — bar-tap + eval-tracker + collapse header

Executed: 2026-11-08 in a real headless Chrome against the built `web/index.html` (and a temp copy for the empty-state case). Method: DOM-driven `MouseEvent('click')`/scroll + text assertions; evidence = returned DOM state and console output.

## bar-detail (tap → number tooltip)

| # | Case | Result | Evidence |
|---|------|--------|----------|
| T1 | Tap newest bar → total tooltip | ✅ PASS | tooltip text = `204`, bar marked tapped |
| T2 | Tap a past bar → recoverable | ✅ PASS | tap idx11 → tooltip `200` (= rolling[11], date 11-06) |
| T3 | Tooltip = bar value, number only | ✅ PASS | tooltip shows exactly the number, no other text |
| T4 | Tap outside clears | ✅ PASS | after outside click, 0 bars marked tapped |

## Collapse header (Slim)

| # | Case | Result | Evidence |
|---|------|--------|----------|
| T5 | Collapse on scroll | ✅ PASS | REAL scroll event (window AND inner container) sets collapsed: header becomes `row`, min-height 74px (avatar left / goal right) |
| T6 | Expand at top | ✅ PASS | REAL scroll to top removes the collapse, header returns to full column + tagline |

## eval-tracker (verdict table)

| # | Case | Result | Evidence |
|---|------|--------|----------|
| T7 | Table renders | ✅ PASS | "Treadmill evals · 15% incline / 30 min" with Date/Efficiency/Work/Status, 5 rows newest-first |
| T8 | HR up + speed same = Regression | ✅ PASS | 30.10 row Status = `Regression` (red) |
| T9 | Speed up / HR down = Improved | ✅ PASS | 29.10/18.08/10.08 Status = `Improved` (green) |
| T10 | First eval = start | ✅ PASS | 05.08 Status = `start` (grey) |
| T11 | Efficiency ✓ when HR down | ✅ PASS | 10.08 `106 ✓` (HR down vs 113) |
| T12 | Work ✓ when speed up | ✅ PASS | 18.08 `3 ✓` (speed up vs 2.5) |
| T13 | Efficiency ✗ when HR up at same speed | ✅ PASS | 30.10 `138 ✗` (regression) |
| T14 | Empty state | ✅ PASS | renders "No evals yet" in a full-width cell, no error |
| T15 | Long-session eligibility (data rule) | ✅ PASS (by construction) | only qualified 30-min-window evals present in EVALS; none unqualified |
| T16 | Console clean | ✅ PASS | 0 console lines/errors on page load |

**Summary: 16/16 pass. No failures.**
Bonus: caught and fixed a real bug during testing — the scroll-collapse initially failed because `.bottom` lacked `id="bottom"`, surfacing as a `null.addEventListener` error that the console check caught.