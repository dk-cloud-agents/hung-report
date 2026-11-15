# Spec Delta

## Purpose

Shows how D K's endurance minutes are made up day by day, so the rolling 7-day total is backed by a per-date view of each logged session's duration.

## ADDED Requirements

### Requirement: Per-day endurance session chart under the rolling total
The system SHALL render a second bar chart beneath the rolling-7-day chart in which each bar represents one calendar date, its height being the total minutes of endurance logged on that date (the sum of all sessions of any endurance type recorded that day).

#### Scenario: Chart shows a bar for each day with logged sessions
- **WHEN** endurance sessions exist on one or more dates
- **THEN** each such date renders a bar whose height equals the sum of that day's session durations, positioned on the date's X-axis slot

#### Scenario: Multiple sessions on the same date are summed into one bar
- **WHEN** more than one endurance session is logged on the same date
- **THEN** the chart shows a single bar for that date whose height is the combined minutes of all those sessions

#### Scenario: Day with no sessions shows no bar
- **WHEN** a date in the series has zero logged endurance minutes
- **THEN** that date shows no bar (zero height), preserving the day's slot on the axis

### Requirement: Data source is the per-day total
The chart SHALL be populated from the per-date total minutes already computed for the report (the `Total (min)` per-day figure), so the bar height always reflects every endurance session on that date regardless of its type — walking, treadmill, or other endurance.

#### Scenario: Any endurance type counts toward the day's bar
- **WHEN** a given date contains a non-walking endurance session (e.g. an outdoor cycle), alone or alongside walking
- **THEN** that session's minutes are included in the date's bar total

### Requirement: Labels match report conventions
The chart SHALL carry a title distinct from the rolling chart's, with the X axis labelled by date and the Y axis indicating minutes; dates SHALL be formatted to the same European `DD.MM` style used elsewhere in the report.

#### Scenario: Chart shows a title and axis labels
- **WHEN** the per-day chart renders
- **THEN** it shows a title, a date axis, and a minutes axis using report-consistent formatting

### Requirement: Empty state
When there is no per-day session data to display, the chart SHALL show an empty-state message instead of nothing or an error.

#### Scenario: No endurance session data
- **WHEN** the per-day series is empty
- **THEN** the chart area shows an empty-state message rather than a broken or blank render

### Requirement: Selecting a rolling bar highlights its 7-day window
The system SHALL let D K tap a rolling-7-day bar to highlight in the daily chart below exactly the 7 days that compose that bar's total — the window ending on the selected bar's own date — using a single black box and the same black emphasis as the selected bar.

#### Scenario: Selecting a rolling bar highlights its seven window days
- **WHEN** D K taps a rolling-7-day bar
- **THEN** the selected bar shows a black outline and a single black box encloses exactly the 7 daily bars that make up that total in the chart below, dimming the daily bars outside it

#### Scenario: Rest days inside the window stay visible
- **WHEN** a day inside the highlighted window had zero logged minutes
- **THEN** its empty bar slot remains visible inside the box (not hidden or dimmed away as if outside the window)

#### Scenario: The window ends on the selected date and includes it
- **WHEN** D K selects a rolling bar for a given date (e.g. 14 Nov)
- **THEN** the highlighted window spans that date back to the 6 preceding days (e.g. 08–14 Nov), ending on and including the selected bar's own date, not a day earlier or later

#### Scenario: No selection on load
- **WHEN** the report first loads
- **THEN** no rolling bar is selected and no box or dimming is shown, until D K taps a bar

#### Scenario: Tapping the selected bar again deselects it
- **WHEN** D K taps the already-selected rolling bar a second time (or taps outside the rolling bars)
- **THEN** the selection clears, the box disappears, and the daily chart returns to its full, un-dimmed display

#### Scenario: Oldest bars clamp to the available history
- **WHEN** D K selects a rolling bar whose seven-day window would start before the oldest recorded day
- **THEN** the highlight covers only the available days from the chart's start through the selected date (never extending before the earliest day)