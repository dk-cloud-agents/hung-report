# Test Plan — bar-tap + eval-tracker

> **APPROVAL NEEDED BEFORE IMPLEMENTATION.** Reviewed by D K; implementation starts only on her approval.
> After implementation every case is filled with REAL results (pass/fail, evidence, timestamp). NA + reason where not runnable. Results are never fabricated.

## bar-detail

| # | Test case | Input | Expected outcome | Status |
|---|-----------|-------|------------------|--------|
| T1 | Tap newest bar | Tap rightmost bar | Readout shows that date's trailing-7-day total | pending |
| T2 | Tap a past bar | Tap an older bar (e.g. 11-05) | Readout shows that past date's total (recoverable) | pending |
| T3 | Readout matches bar | Tap bar at value T | Readout shows exactly T, sourced from `rolling[]` | pending |
| T4 | Tap outside bars | Tap chart area but not a bar | No readout change / open readout clears | pending |

## eval-tracker

| # | Test case | Input | Expected outcome | Status |
|---|-----------|-------|------------------|--------|
| T5 | Rows render | Page with EVALS populated | Section "Treadmill evals" shows rows newest-first with date, HR, speed | pending |
| T6 | Trend tags | Two+ evals present | Each non-first row has separate HR + speed trend tags vs previous | pending |
| T7 | Flat tag | Two evals same speed (29.10→30.10) | That axis tagged → | pending |
| T8 | Empty state | Page with no EVALS | Shows "No evals yet", no error | pending |
| T9 | Long-session eligibility | A >30-min eval without 30-min-window HR present in data | Excluded from trend (documented, not shown as eval) | pending |

## Execution rule

- T1–T8 run in a real browser against the deployed `web/index.html` (tap simulation or JS-driven click).
- Every result carries evidence (screenshot/console output) + timestamp.
- T9 is a data-eligibility check: verify no unqualified longer session appears in the list.