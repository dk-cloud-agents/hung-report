# Spec Delta

## Purpose

Lets the user recover any bar's trailing-7-day total with a tap. Today only the newest total is shown in the metric; a past week's sum is implied by bar height alone and cannot be retrieved, which makes historical review impossible. A tap surfaces the exact number for any bar.

## ADDED Requirements

### Requirement: Tapping a bar shows that date's 7-day total as a tooltip
The system SHALL show, on a single tap of any bar, that bar's date's trailing-7-day total minutes as a number-only tooltip anchored above the tapped bar, for both current and historical bars.

#### Scenario: Tap the newest bar
- **WHEN** the user taps the rightmost bar
- **THEN** a tooltip appears above it showing only that date's trailing-7-day total number

#### Scenario: Tap a past bar
- **WHEN** the user taps a bar older than today
- **THEN** the tooltip shows that past date's trailing-7-day total (recoverable even though the number is not otherwise in the UI)

### Requirement: Tooltip matches the bar's value
The tapped tooltip SHALL equal the 7-day total that bar represents, shown as the bare number with no other text; the sum SHALL be sourced from the same `rolling` array used to draw the bars.

#### Scenario: Tooltip equals the bar's value
- **WHEN** the user taps a bar whose drawn height corresponds to total T
- **THEN** the tooltip shows exactly the number T and nothing else

### Requirement: Tap-off clears or is inert
Tapping outside any bar SHALL clear the readout or produce no change; it SHALL not show stale or wrong data.

#### Scenario: Tap outside bars
- **WHEN** the user taps the chart area but not on a bar
- **THEN** no readout changes, or an open readout closes

## ADDED Requirements

### Requirement: Header collapses on scroll (Slim)
The page header (avatar + goal metric + tagline) SHALL shrink on vertical scroll of the content into a single horizontal line — small avatar on the left, the goal readout on the right — so the scrollable content has more room. On returning to the top it SHALL expand back.

#### Scenario: Scrolled down
- **WHEN** the content is scrolled down
- **THEN** the header collapses to a single line: small avatar left, goal right, tagline hidden

#### Scenario: Back at top
- **WHEN** the content is scrolled back to the top
- **THEN** the header expands to its full avatar + number + tagline state