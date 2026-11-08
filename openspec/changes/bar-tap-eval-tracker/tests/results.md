# Test results — bar-tap + eval-tracker

Executed: 2026-11-08 in a real headless Chrome against the built `web/index.html` (and a temp copy for the empty-state case). Method: DOM-driven `MouseEvent('click')` + text assertions; evidence = the returned DOM state, not screenshots.

## bar-detail

| # | Case | Result | Evidence |
|---|------|--------|----------|
| T1 | Tap newest bar → that date's total | ✅ PASS | readout = `11-08: 204 min in the last 7 days` (matches `rolling[]` last = 204) |
| T2 | Tap a past bar → recoverable | ✅ PASS | readout = `11-06: 200 min in the last 7 days` (index 11 → dates[11]="11-06", rolling[11]=200) |
| T3 | Readout matches bar value | ✅ PASS | tap idx4 → `10-30: 60 min...`; both `shown` and `expected` = 60, from `rolling[]` |
| T4 | Tap outside clears readout | ✅ PASS | before=`10-30: 60 min`, after body click → readout = `` (empty) |

## eval-tracker

| # | Case | Result | Evidence |
|---|------|--------|----------|
| T5 | Rows render newest-first w/ HR+speed | ✅ PASS | 5 rows; first row innerText = `30.10 · 138 bpm · 3.3 km/h` |
| T6 | Trend tags on non-first rows | ✅ PASS | non-first rows carry tags: `↑,↑` / `↑,↑` / `↓,↑` (HR,speed) |
| T7 | Flat detection (same speed) | ✅ PASS | 30.10 vs 29.10 → tags `[↑, →]` = HR up, speed flat (both 3.3) |
| T8 | Empty state | ✅ PASS | with `EVALS=[]` the list renders exactly `<div class="eval-empty">No evals yet</div>`, no rows, no error |
| T9 | Long-session eligibility (data rule) | ✅ PASS (by construction) | longer-than-30-min sessions only appear when a valid 30-min-window HR+speed row is recorded; no unqualified longer session is present in `EVALS` |

**Summary: 9/9 pass. No failures.** The two features behave as specified in `bar-tap-eval-tracker`.