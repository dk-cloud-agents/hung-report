# Test Plan — zone-2-filter

> **APPROVAL NEEDED BEFORE IMPLEMENTATION.** Reviewed by D K; implementation starts only on her approval.
> After implementation every case is filled with REAL results (pass/fail, evidence, timestamp). NA + reason where not runnable. Results are never fabricated.

## zone-2-filter (UI filter)

| # | Test case | Input | Expected outcome | Status |
|---|-----------|-------|------------------|--------|
| T1 | Control renders | Load report | "Total \| Strict Z2" segmented control visible above the charts | pending |
| T2 | Default OFF | Fresh load | Headline + both charts show total endurance; control reads "Total" | pending |
| T3 | Apply filter | Click "Strict Z2" | Headline flips to the strict-Z2 7-day value; rolling + daily charts re-render from Z2 data | pending |
| T4 | Filtered values match the sheet | Compare filtered headline to sheet col E (7-day Zone 2 Total) for the same date | Values equal | pending |
| T5 | Daily chart filtered | Filter ON | Each daily bar equals sheet col C (per-day Zone 2); rest days show an empty slot | pending |
| T6 | Remove filter | Click "Total" | Headline + charts return to total endurance values | pending |
| T7 | Eval tracker unaffected | Filter ON, inspect eval table | Eval rows unchanged | pending |
| T8 | No persistence | Apply filter, reload page | Control back to "Total"; total endurance view | pending |
| T9 | Served data arrays | grep the published page | `const z2rolling` / `const z2daily` present with numeric values | pending |
| T10 | Console clean | Load + toggle both ways | 0 console errors | pending |

## Execution rule

- T1–T10 run in a real browser against the deployed `web/index.html` (real click events, DOM-state assertions; console errors captured).
- UX sign-off recorded in `tests/results.md` before implementation starts (mockup `mockups/zone2-filter-mockup.html`).
