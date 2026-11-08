# Spec Delta

## Purpose

Surfaces the eval-session progression that proves cardiovascular improvement. The app's main chart shows weekly volume only; it has no view of the treadmill eval-tests (15% incline / 30 min) where speed-up at fixed grade and HR trend are read. A dedicated list under the chart makes that trend visible.

## ADDED Requirements

### Requirement: Eval sessions listed under the chart
The system SHALL show a list of treadmill eval-sessions beneath the main chart, newest first, each row presenting the date, the average heart rate, and the speed.

#### Scenario: Eval rows with HR + speed
- **WHEN** eval-session data exists
- **THEN** each eval row shows its date, average HR, and speed on the 15%-incline/30-min protocol

#### Scenario: No evals recorded
- **WHEN** no eval rows exist
- **THEN** the section shows an empty-state message rather than nothing or an error

### Requirement: Trend tag per axis vs previous eval
The system SHALL indicate, for each eval row after the first, whether its average HR and speed trended up (↑), down (↓), or stayed flat (→) relative to the previous eval, kept as separate tags for HR and speed.

#### Scenario: HR and speed both trend
- **WHEN** the user reviews two consecutive evals
- **THEN** the newer row carries one trend tag for HR and one for speed, each comparing against the previous row

#### Scenario: Flat comparison
- **WHEN** an eval's HR or speed equals the previous within a small tolerance
- **THEN** that axis is tagged → (flat), not ↑ or ↓

### Requirement: Eval eligibility — strict protocol, valid 30-min window
An eval SHALL count only if it was a 15%-incline session with HR data for a valid 30-minute window. A longer session SHALL count only if the straight-30-min-window average HR and the incumbent speed are recorded; otherwise any such session is excluded from the trend line.

#### Scenario: Standard 30-minute eval
- **WHEN** a session is exactly 30 min at 15% incline with a recorded average HR
- **THEN** it appears on the eval tracker

#### Scenario: Longer session with a valid 30-min window
- **WHEN** a session is longer than 30 min and the 30-min-window average HR and speed are recorded
- **THEN** it appears on the eval tracker using those window values

#### Scenario: Longer session without the window values
- **WHEN** a longer session lacks the 30-min-window average HR
- **THEN** it is excluded from the eval trend and is not presented as an eval