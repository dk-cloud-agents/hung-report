# Spec Delta

## Purpose

Lets the user recover any bar's trailing-7-day total with a tap. Today only the newest total is shown in the metric; a past week's sum is implied by bar height alone and cannot be retrieved, which makes historical review impossible. A tap surfaces the exact number for any bar.

## ADDED Requirements

### Requirement: Tapping a bar shows that date's 7-day total
The system SHALL show, on a single tap of any bar, that bar's date's trailing-7-day total minutes as a clear numeric readout, for both current and historical bars.

#### Scenario: Tap the newest bar
- **WHEN** the user taps the rightmost bar
- **THEN** a readout appears showing that date's trailing-7-day total

#### Scenario: Tap a past bar
- **WHEN** the user taps a bar older than today
- **THEN** the readout shows that past date's trailing-7-day total (recoverable even though the number is not otherwise in the UI)

### Requirement: Readout matches the floated bar value
The tapped readout SHALL equal the 7-day total that bar represents; the sum SHALL be sourced from the same `rolling` array used to draw the bars.

#### Scenario: Readout equals the bar's value
- **WHEN** the user taps a bar whose drawn height corresponds to total T
- **THEN** the readout shows T and no other number

### Requirement: Tap-off clears or is inert
Tapping outside any bar SHALL clear the readout or produce no change; it SHALL not show stale or wrong data.

#### Scenario: Tap outside bars
- **WHEN** the user taps the chart area but not on a bar
- **THEN** no readout changes, or an open readout closes