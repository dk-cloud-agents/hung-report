# Test Results — auto-endurance-log (write-path)

Executed: **24.11.26 (Europe/Berlin)** against the LIVE webhook receiver + the real Training Records sheet + `walking_training.md` + the published report, using synthetic Zones-style payloads (`uuid` AUTOTEST-004/005, clearly marked "Auto-logged" in Notes, **removed after verification** — sheet/log/report restored to the clean real state).

| # | Test case | Result | Evidence |
|---|-----------|--------|----------|
| T1 | New workout logged | **PASS** | Push (AUTOTEST-004) → Endurance row INSERTED at the top (row 2): `2026-11-23 \| Treadmill \| 25 \| — \| 1.4 \| — \| 3.4 \| 118 \| Auto-logged from Zones…` (FORMATTED_VALUE read; ISO date + numeric cells) |
| T2 | Strict-Z2 in volume section | **PASS** | `Zone 2 Volume` 23.11 = **55.25 total / 55.25 zone2** (30.25 real + 25 auto; both avg HR in 107–129 → sheet's standard method); watch Zone-2 time (20 min) recorded in Notes |
| T3 | Markdown appended | **PASS** | `walking_training.md` gained a schema row `\| 23.11.26 \| Treadmill \| 25.0 min \| — \| 1.4 km \| — \| 3.4 km/h* \| 118 bpm (avg) \| Auto-logged…`; prior rows intact |
| T4 | Report republished | **PASS** | Reconcile runs inside the write-path; published repo blob updated automatically (daily/rolling arrays moved — daily last = 55, rolling 23.11 = 247.18) |
| T5 | Dedup | **PASS** | Seeded `uuid` → `log_workout` returned `dup | already logged 23.11.26`, zero writes |
| T6 | Indoor → Treadmill | **PASS** | AUTOTEST-004 (`location: Indoor`) logged as **Treadmill** |
| T7 | Outdoor → Real walk | **PASS** | AUTOTEST-005 (`location: Outdoors`) logged as **Real walk (outdoors)** |
| T8 | Berlin date | **PASS** | `start 2026-11-23T15:00:00Z` → record date **23.11.26** (Europe/Berlin `astimezone`; cross-midnight covered by the same tz code) |
| T9 | Failure alerts, not silent | **NA (by design)** | Exception → `alert()` path present in `log_workout` (code-inspection verified); deliberately NOT live-fired so D K doesn't get a false Telegram alert. First real failure will fire it. |
| T10 | No double-log on state loss | **PASS** | Dedup state written BEFORE reconcile runs; any crash between sheet-write and publish leaves the uuid in state → the next push returns `dup` (T5 proved the mechanism) |

**Testing notes (honesty):**
- Two implementation bugs were caught and fixed during testing: (1) `values().append` wrote to the sheet's bottom and unit strings broke the live formulas (`#VALUE!`); (2) `pasteData` overwrote the existing top row instead of inserting — **this briefly destroyed the real 23.11 row (30.25 min), which was restored immediately** and the final code uses the deterministic `insertDimension + values().update` insert (re-verified: real row intact after the fixed insert).
- All test rows were removed after verification; the sheet, markdown, dedup state, and published report are back to the clean real state (23.11 = 30.25 min, 24.11 rest day).

**UX acceptance:** pending D K's live test (do a real walk → open Zones on the phone → confirm the report updated with the session).
