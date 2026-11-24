# Design

## Context

The spike (SCRUM-9) already parses a pushed workout into a dict: `type`, `start`/`end` (ISO-8601 UTC), `duration_min`, `distance_km`, `calories`, `avg_bpm`/`max_bpm`, `zones_sec` (zone1..5), and `strict_z2_min` (mapped from `zones.zone2.time`). The write-path adds the "log it + update the report" step that the manual flow already knows how to do. See the manual flow's rules in the fitness skill's `sheets-sync` reference (newest-first Endurance rows, live formulas in Zone 2 Volume, deterministic rebuild) and `hung-report-pipeline` (publish + silent-failure alerting).

## Goals / Non-Goals

**Goals:** on each pushed, non-duplicate workout — write an Endurance row, update Zone 2 Volume, append to `walking_training.md`, then republish the report. Reuse the existing sheet-sync + publish code paths.

**Non-Goals:** no report UI/design change; no change to the sheet schema or the strict-Z2 def (still Zone 2 time = 107–129); no handling of strength/1RM (endurance only).

## Decisions

1. **One new module, not scattered edits.** Add `scripts/ingest_workout.py` with `log_workout(parsed)` that: (a) dedup-checks a state file `/opt/data/.logged_workouts.json` keyed by workout `uuid` (falling back to start-time), (b) writes, (c) publishes, (d) alerts on failure. `receiver.py` calls it after a successful parse. Keeps the webhook thin and the step testable standalone.
2. **Reuse the sheet-sync + publish code.** The Endurance insert and the Zone 2 Volume update reuse the same google-sheets calls as the manual flow (newest-first insert at top; Zone 2 Volume rebuilt deterministically like `reconcile_daily.py` so live-formula anchors stay correct). Publishing calls the existing `publish_report.py` (venv python). We do not re-invent the pipelines.
3. **Dedup file over DB.** A small JSON state file (uuid → logged timestamp) survives restarts and costs nothing; the workout `uuid` from the Zones JSON is a stable identity. Considered a sheet/db lookup first but rejected (extra sheet reads per push; a tiny local file is enough).
4. **Convention mapping in one place.** Ingest maps: `location == "Indoor"` → type `Treadmill`, else → `Real walk (outdoors)`; `start` UTC → `Europe/Berlin` for the record date (budget the same DST-aware rule the pipeline uses); strict-Z2 already computed from `zones.zone2.time`.
5. **Alert, don't go silent.** Any write/publish exception alerts D K via the existing `alert.py` (dedup'd cooldown), matching the silent-failure rule — a broken write must never look like "nothing new arrived."

## Risks / Trade-offs

- [Duplicate or mis-fire push] → dedup state file + type/timezone mapping make repeats idempotent; a wrong-session push is manually removable (existing sheet is hand-editable).
- [Sheet write fails (token)] → alert fires with the cause; markdown + state still updated so nothing is silently lost.
- [Zones' zone2 ≠ exact HR band] → accepted trade-off: Zone 2 time is the configured 107–129 band she already chose; consistent with the report's strict-Z2 filter.
- [Watch-alone open won't trigger] → out of scope (UX decision recorded in the proposal: the phone-open is the trigger).

## Open Questions

None — behavior, approach, and scope are settled; only D K's spec/user-story approval gates implementation.