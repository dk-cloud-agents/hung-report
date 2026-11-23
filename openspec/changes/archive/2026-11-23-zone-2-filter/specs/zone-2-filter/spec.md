# Spec Delta

## Purpose

Lets D K switch the report's volume view (headline number, 7-day rolling chart, and daily chart) between total endurance minutes and strict Zone 2 minutes, so she can judge her week against Attia's Zone 2 guidelines.

## ADDED Requirements

### Requirement: Volume view filter, applicable and removable
The system SHALL provide a UI filter control that switches the report's volume display between total endurance minutes and strict Zone 2 minutes, SHALL let D K apply and remove the filter, and SHALL visibly indicate which state is active.

#### Scenario: Apply the filter
- **WHEN** D K turns the filter on
- **THEN** the volume surfaces show strict Zone 2 minutes and the control reads as on

#### Scenario: Remove the filter
- **WHEN** D K turns the filter off again
- **THEN** the volume surfaces show total endurance minutes (today's behavior) and the control reads as off

### Requirement: Filter flips headline and both charts together
When the filter is ON, the headline number ("X min / 180 goal"), the 7-day rolling chart, and the daily endurance-minutes chart SHALL all display strict Zone 2 values, driven by a single control.

#### Scenario: All three surfaces flip together
- **WHEN** the filter is applied
- **THEN** the headline number, the rolling-7-day bars, and the daily bars all show strict Zone 2 minutes

#### Scenario: Eval tracker unaffected
- **WHEN** the filter is applied
- **THEN** the treadmill eval table continues to show eval sessions unchanged

#### Scenario: Goal line unchanged
- **WHEN** the filter is applied
- **THEN** the headline's 180-minute goal remains the comparison target

### Requirement: Strict Zone 2 data comes from the sheet's computed band
The filtered values SHALL come from the strict Zone 2 figures already computed in the report's data source (per-day Zone 2 minutes and their 7-day rolling total, live-formula columns based on the current HR 107–130 Zone 2 band), with no re-computation in the report itself.

#### Scenario: Filtered values match the sheet's Zone 2 columns
- **WHEN** the filter is applied on a given date
- **THEN** the shown strict Zone 2 minutes equal the source sheet's per-day Zone 2 and 7-day Zone 2 Total values for that date, rounded to the nearest whole minute (consistent with how total volume is displayed)

#### Scenario: Rest day shows no strict Zone 2 bar
- **WHEN** a date in the series has zero strict Zone 2 minutes
- **THEN** that date shows no bar in the filtered daily chart, preserving its axis slot

### Requirement: Default state is total endurance, not remembered
The report SHALL load with the filter off (total endurance view) on every visit, and SHALL NOT persist the filter choice between loads.

#### Scenario: Reload resets to total endurance
- **WHEN** the page loads after the filter was previously applied
- **THEN** the volume surfaces show total endurance minutes and the filter control reads as off
