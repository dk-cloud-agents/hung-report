# Spec Delta

## Purpose

Surfaces the eval-session progression that proves cardiovascular improvement. The app's main chart shows weekly volume only; it has no view of the treadmill eval-tests (15% incline / 30 min) where speed-up at fixed grade and HR trend are read. A dedicated verdict table under the chart makes that trend visible and readable at a glance.

## ADDED Requirements

### Requirement: Eval sessions shown as a verdict table
The system SHALL show treadmill eval-sessions beneath the main chart as a table (newest first) with columns: Date, Efficiency (avg HR), Work (speed), and Status (an overall improvement/regression verdict). The table header SHALL be "Treadmill evals · 15% incline / 30 min".

#### Scenario: Table shows date, HR, speed, status
- **WHEN** eval-session data exists
- **THEN** the table renders each eval with its date, average HR (Efficiency), speed (Work), and an overall Status column, newest first

#### Scenario: No evals recorded
- **WHEN** no eval rows exist
- **THEN** the table shows an empty-state message rather than nothing or an error

### Requirement: Efficiency and Work glyphs coloured by true goodness
The Efficiency and Work cells SHALL each carry a glyph whose colour means good/bad, not direction: HR down (Efficiency improving) = green ✓; HR up = red ✗; HR flat = grey →. Speed up (Work improving) = green ✓; speed down = red ✗; speed flat = grey →.

#### Scenario: Efficient but same work
- **WHEN** HR goes down relative to the previous eval at the same speed
- **THEN** Efficiency shows green ✓ and Work shows grey →

#### Scenario: Same efficiency, flat work
- **WHEN** HR and speed both match the previous eval
- **THEN** both cells show grey →

### Requirement: Status column conveys improvement vs regression
The Status column SHALL present a single overall verdict per eval — green "Improved", red "Regression", or grey "Steady"/"start" — computed from both axes, so "good" and "bad" are unambiguous regardless of which way the raw numbers moved.

#### Scenario: HR up at same speed is regression
- **WHEN** an eval's HR rises while speed is unchanged versus the previous eval
- **THEN** Status shows "Regression" in red (the same-speed/higher-HR case is a performance regression, not an improvement)

#### Scenario: Speed up with HR not rising is improvement
- **WHEN** an eval's speed rises while HR is the same or lower versus the previous eval
- **THEN** Status shows "Improved" in green

#### Scenario: First eval has no comparator
- **WHEN** only one eval exists
- **THEN** Status shows "start" in grey with no comparison

### Requirement: Eval eligibility — strict protocol, valid 30-min window
An eval SHALL count only if it was a 15%-incline session with HR data for a valid 30-minute window. A longer session SHALL count only if the straight-30-min-window average HR and the incumbent speed are recorded; otherwise any such session is excluded from the table.

#### Scenario: Standard 30-minute eval
- **WHEN** a session is exactly 30 min at 15% incline with a recorded average HR
- **THEN** it appears in the eval table

#### Scenario: Longer session with a valid 30-min window
- **WHEN** a session is longer than 30 min and the 30-min-window average HR and speed are recorded
- **THEN** it appears in the eval table using those window values

#### Scenario: Longer session without the window values
- **WHEN** a longer session lacks the 30-min-window average HR
- **THEN** it is excluded from the eval table and is not presented as an eval