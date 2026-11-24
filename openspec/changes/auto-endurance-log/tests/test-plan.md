# Test Plan — auto-endurance-log (write-path)

> **APPROVAL NEEDED BEFORE IMPLEMENTATION.** Reviewed by D K; implementation starts only on her approval.
> After implementation every case is filled with REAL results (pass/fail, evidence, timestamp). NA + reason where not runnable. Results are never fabricated.

## endurance-auto-log (write-path)

| # | Test case | Input | Expected outcome | Status |
|---|-----------|-------|------------------|--------|
| T1 | New workout logged | Push a novel Zones-style workout to the webhook | An `Endurance`-tab row appears (date, type, duration, distance, avg HR) matching the payload | pending |
| T2 | Strict-Z2 in volume section | Same push with avg HR in band | `Zone 2 Volume` per-day + 7-day totals include the session (avg-HR-in-band method, consistent with manual logs); watch zone2 minutes appear in Notes | pending |
| T3 | Markdown appended | Same push | A row is appended to `walking_training.md` in the report schema; prior rows intact | pending |
| T4 | Report republished | After a write | The live report's served data arrays reflect the new session (no manual publish) | pending |
| T5 | Dedup | Push the same workout again (same uuid) | No second Endurance row, no second log entry, no extra publish | pending |
| T6 | Indoor → Treadmill | Push with `location:"Indoor"`, name Walking | Session type recorded as `Treadmill` | pending |
| T7 | Outdoor → Real walk | Push with outdoor/absent location | Session type recorded as `Real walk (outdoors)` | pending |
| T8 | Berlin date | Push a workout with a UTC start near midnight Berlin | Record date is the Europe/Berlin date, not the UTC day | pending |
| T9 | Failure alerts, not silent | Force the sheet token to fail during a write | D K receives an alert with the cause; (no fabricated "logged" row) | pending |
| T10 | No double-log on state loss | Simulate a crash between sheet-write and markdown-write | On re-run the dedup state prevents writing the same session a second time | pending |

## Execution rule

- T1–T10 run for real against the live webhook + Training Records sheet + `walking_training.md` + published report, using a synthetic-but-realistic workout payload (clearly marked as a test, removable).
- Every result carries evidence (row content / state-file entry / alert transcript) + timestamp.
- T9 verifies BOTH the alert fires AND it dedups (state file written).
- UX approval: D K reviews the documented UX flow (proposal) and the user story before implementation.