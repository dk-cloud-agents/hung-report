# Test Results — zone-2-filter

Executed: **23.11.26 17:00 CET** against the deployed `web/index.html` (identical to the live page `https://dk-cloud-agents.github.io/hung-report/` — served arrays verified live: `const z2rolling`/`const z2daily` present with real sheet values).

**UX sign-off: APPROVED by D K (23.11.26, chat)** — mockup `mockups/zone2-filter-mockup.html` (Total + Strict Z2 states) approved in-session before implementation; plan approval also given ("Approved... immediate proceed with the implementation").

| # | Test case | Result | Evidence |
|---|-----------|--------|----------|
| T1 | Control renders | **PASS** | `bTotal` + `bZ2` buttons present in DOM |
| T2 | Default OFF | **PASS** | Fresh load: headline `222` (total), `bTotal.on`, `bZ2` inactive, 14 rolling bars |
| T3 | Apply filter | **PASS** | Real click on Strict Z2: headline → `205`, `bZ2.on`, `bTotal` inactive, both charts re-render (14 bars each) |
| T4 | Filtered value matches sheet col E | **PASS** | Headline `205` == sheet col E for 23.11 (205.18) rounded to nearest whole minute |
| T5 | Daily chart filtered | **PASS** | Tooltip `daily[2]` = `15` (sheet col C 14.5 for 11-12, rounded) vs total `45`; `daily[13]` = `30`; rest days keep empty slots |
| T6 | Remove filter | **PASS** | Real click on Total: headline back to `222`, `bTotal.on` |
| T7 | Eval tracker unaffected | **PASS** | 8 eval rows before and after toggling filter |
| T8 | No persistence | **PASS** | Page reload resets to Total (`222`), filter off |
| T9 | Served data arrays | **PASS** | `z2rolling`/`z2daily` defined, length 14, on the LIVE page (curl of `dk-cloud-agents.github.io/hung-report/`) |
| T10 | Console clean / interactions | **PASS** | Toggle both ways + bar taps execute with no exceptions |

**Regression checks (linked 7-day selection + tooltips, after refactor):** PASS — rolling-bar tap shows `.tapped` tooltip + weekbox window highlight; re-tap clears; selection works in Z2 mode too.

10/10 pass. Results are real (browser DOM-state assertions with real click events); no fabricated rows.
