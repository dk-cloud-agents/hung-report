# Proposal

## Why

The fitness app shows the trailing-7-day total as bars but exposes no detail: past weeks' sums are only implied by bar height and can't be recovered, and the user has no view of her **eval-session progression** (the 15%-incline/30-min treadmill tests that actually prove she's improving). D K wants (1) a tap on any bar to reveal that week's total, and (2) a dedicated eval tracker under the chart so the speed/HR trend is visible.

## What Changes

- **New capability `bar-detail`**: tapping a bar in the rolling-7-day chart reveals that date's trailing-7-day total as a clear readout. Works for any bar (past and present) — past sums are otherwise unrecoverable from the UI.
- **New capability `eval-tracker`**: under the chart, a list of treadmill eval-sessions — each `date → avg HR @ speed`, on the 15%-incline/30-min protocol — with a trend tag (↓/→/↑ per axis vs the previous eval), newest first.
- **On-log publish (process)**: the every-30-min poll cron was removed; publishing now fires on (a) each logged-and-synced session and (b) the daily 00:01-Berlin rollover. `publish_report.py` stages only `index.html` (OpenSpec/plan files stay out of auto-commits).

## Capabilities

- **New**: `bar-detail`
- **New**: `eval-tracker`

## Impact

- Extends `web/index.html` (the single published file); no change to the Google Sheet schema or the markdown source.
- Eval data source: the existing treadmill rows in the Training Records sheet / walking log (29.10, 30.10 at 3.3 km/h; 5/10/18.8 at 2.4–3.0 km/h). New evals must record the **30-min-window avg HR + speed** even when the raw session was longer.
- Replaces the retired `hung-report-auto-publish` 30-min cron.
- **BREAKING** none.