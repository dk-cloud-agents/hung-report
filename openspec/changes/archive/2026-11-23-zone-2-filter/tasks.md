# Tasks

## 1. Data pipeline (publish_report.py)

- [x] 1.1 Extend `read_weekly_series()` to read `Zone 2 Volume!A2:E60`, capturing col C (per-day Z2) and col E (7-day Z2 rolling) alongside today's A/B/D; verify both new values parse as numbers for every row (run the read, print first/last rows).
- [x] 1.2 Emit `const z2daily` and `const z2rolling` arrays via the existing re.sub pattern and include them in the `sig` JSON; verify a run with changed Z2 data republishes and an unchanged run stays silent (no new commit).

## 2. UX mockup + sign-off (gate)

- [x] 2.1 Interactive mockup `mockups/zone2-filter-mockup.html` (segmented pill, both states) built and rendered to PNG during propose; present to D K and record her approval in `tests/results.md` before implementation.

## 3. UI filter (template)

- [x] 3.1 Add the "Total | Strict Z2" segmented control to the template above the charts; verify it renders and both segments are clickable (DOM assertions in a real browser).
- [x] 3.2 Parametrize the bar renderers and the headline setter to take a data array; on toggle re-render headline + rolling chart + daily chart from the active set; verify the DOM numbers change on toggle and match the injected arrays.
- [x] 3.3 Default state OFF on load, no persistence; verify a page reload resets the control and the view (no localStorage/URL state used).

## 4. Tests

- [x] 4.1 `tests/test-plan.md` exists with all rows pending (approval-gated before implementation).
- [x] 4.2 Execute the test plan in a real browser against the deployed page (real click events, DOM-state assertions); fill `tests/results.md` with real pass/fail + evidence + timestamps; run `openspec validate --changes zone-2-filter` until clean.

## 5. Ship

- [x] 5.1 Sync template → `web/index.html`, commit scoped (`git add -- index.html`), push; verify the LIVE page's served arrays include `z2rolling`/`z2daily` (grep the published page).
- [x] 5.2 Present the live link + test report to D K (paired, per SDD rule); on her acceptance run `openspec archive zone-2-filter` and mark SCRUM-8 Done.

## Workflow follow-up

- Update the fitness skill's hung-report-pipeline reference with the two new arrays and the filter behavior.
- Mark SCRUM-8 Done in JIRA after D K's acceptance (label `hungs-fitness-report`).