# Design

## Context

`publish_report.py` reads `Zone 2 Volume!A2:D60` (date A, per-day total B, 7-day rolling total D) and injects `dates`/`rolling`/`daily` arrays into the template; the headline (`#num`/`#goal`), rolling chart (`#bars`), and daily chart (`#barsD`) render from those arrays. The sheet already computes strict Zone 2 as **live formulas** (verified 23.11.26): col C per-day `=SUMPRODUCT((Endurance!$H…>=107)*(Endurance!$H…<=130)*(duration))`, col E 7-day `=SUM(C…)`. The report simply never reads them. Motivation: see proposal.md.

## Goals / Non-Goals

**Goals:** flip headline + both charts to strict Zone 2 via a UI toggle; no sheet schema change; no persistence; eval tracker untouched.

**Non-Goals:** no change to the zone-band definition (the sheet formula owns it); no per-session drill-down; no change to the 180-minute goal logic.

## Decisions

1. **Data transport — extend the existing read, add two arrays.** `read_weekly_series()` reads `A2:E60`, captures col C → `z2daily[]` and col E → `z2rolling[]` alongside today's values; `publish_report.py` re.subs two more `const` arrays and adds them to the `sig` JSON so a strict-Z2-only change still republishes. *Alternative rejected:* recomputing the band in JS — the sheet formula is the single source of truth for the band (107–130); duplicating it in the app would drift.
2. **UI control — segmented pill "Total | Strict Z2" above the charts**, next to the "Rolling 7-day total" title (D K's chosen placement). Active segment highlighted; state is a plain JS variable, default "total", nothing persisted.
3. **Rendering — parametrize the existing renderers.** The bar renderers and the headline setter take a data-array argument; on toggle they re-render from the active set (total vs Z2). No layout or chart-geometry changes; filtered bars reuse the same bar/axis code.
4. **UX gate — mockup ships in this change.** Interactive mockup `mockups/zone2-filter-mockup.html` (same HTML/CSS language as the final control) reviewed and approved by D K before implementation; what she approves is what ships — no design-to-code drift.

## Risks / Trade-offs

- [Z2 band definition changes later] → the sheet formula is the only place the band lives; the report merely mirrors cols C/E.
- [Eval read failure] → existing behavior preserved: `read_evals()` already degrades to empty without failing the report.
- [Z2 rolling value is 0 for early history] → col E is a live formula; 0 renders as a 0-height bar exactly like a rest day.
- [sig grows with two arrays] → arrays are small; the unchanged-fast-path still works.

## Open Questions

None — spec, approach, and tasks are settled; the only outstanding item is the UX sign-off on the mockup, which is an approval gate, not a design unknown.
