# Tasks

## 1. Ingest module + dedup (`scripts/ingest_workout.py`)

- [ ] 1.1 Build `log_workout(parsed)` reading `/opt/data/.logged_workouts.json` (loaded, checked, saved); skip any workout whose `uuid` (fallback start-time) is already present; verify a seen uuid returns without writing.
- [ ] 1.2 Wire `receiver.py` to call `log_workout()` after a successful parse; verify a parsed workout is handed to it and a repeat push is skipped.

## 2. Convention mapping

- [ ] 2.1 Map `location=="Indoor"` → type `Treadmill`, else `Real walk (outdoors)`; `start` UTC → Europe/Berlin date; verify each via a unit-style check.
- [ ] 2.2 Take strict-Z2 from `zones.zone2.time`; verify `strict_z2_min` equals zone2 sec ÷ 60.

## 3. Sheet write (Endurance + Zone 2 Volume)

- [ ] 3.1 Append the session to the `Endurance` tab (newest-first, correct columns: date, type, duration, distance, avg HR, notes with strict-Z2 + source) via the existing google-sheets path; verify the row appears with live values.
- [ ] 3.2 Rebuild the `Zone 2 Volume` section deterministically (like `reconcile_daily.py`) so the session's strict-Z2 lands in the per-day and 7-day totals; verify the total moved by the expected amount.

## 4. Markdown + publish

- [ ] 4.1 Append the session row to `walking_training.md` in the report's schema, preserving existing rows; verify append-only.
- [ ] 4.2 After a write, run the existing `publish_report.py` (venv python) so the report regenerates + deploys; verify the live served arrays updated.

## 5. Alerting

- [ ] 5.1 Wrap the write/publish in the existing `alert.py` pattern so a failure pings D K (not silent); verify a forced token failure alerts and the state file makes it not double-log.

## 6. Tests + validate

- [ ] 6.1 `tests/test-plan.md` exists with all rows pending (approval-gated before implementation). Execute the plan after implementation; fill `tests/results.md` with real outcomes/evidence/timestamps; run `openspec validate --changes auto-endurance-log` until clean.

## 7. Ship

- [ ] 7.1 Present live result + test report to D K (paired); on her acceptance run `openspec archive auto-endurance-log` and mark SCRUM-10 Done.

## Workflow follow-up

- Update the fitness skill's pipeline/sheets references with the ingest path and dedup rule.
- Mark SCRUM-10 Done in JIRA after D K's acceptance.