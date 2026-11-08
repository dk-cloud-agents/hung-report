# Tasks

## Feature 1 — bar-detail (tap to reveal 7-day total)

- [ ] Add a `click` handler to each `.bar` that reads its date + value from the aligned `dates[]`/`rolling[]` arrays.
- [ ] Render a readout (overlay or line above chart): `<date>: <value> min in the last 7 days`.
- [ ] Add a document-level click handler so tapping outside a bar clears/ignores the readout.
- [ ] Confirm the number shown always matches the tapped bar's drawn value (same `rolling[]` source).

## Feature 2 — eval-tracker (list under the chart)

- [ ] Add the `EVALS[]` data array (date, avg HR, speed) seeded from the 5 existing treadmill evals.
- [ ] Render a titled section under the chart: "Treadmill evals · 15% / 30 min", newest first, each row `date · HR bpm @ speed km/h`.
- [ ] Implement per-axis trend tags (↑/↓/→) vs the previous eval for HR and speed, with stated tolerances.
- [ ] Add an empty-state message when `EVALS` is empty.

## Test plan (approve first, then execute)

- [ ] Draft `tests/test-plan.md` mapping the REQ/AC to concrete cases (below).
- [ ] Execute against the real page in a browser; fill `tests/results.md` with real pass/fail + evidence.
- [ ] Run `openspec validate` on the change until clean.

## Ship

- [ ] Update the template + `web/index.html`; verify the page renders in a browser.
- [ ] Commit + push (scoped `git add index.html` for the report; plan files in their own commit).
- [ ] Present results to D K; on approval `openspec archive`.
- [ ] Record the eval data-entry rule in the fitness skill (log the 30-min-window avg HR + speed for new evals).