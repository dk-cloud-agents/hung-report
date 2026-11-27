# Spec Delta

## Purpose

Turns an automatically-pushed workout (sent by D K's phone when she opens the Zones app) into a logged endurance session in her training records, and refreshes the report — no manual logging or copy-paste.

## ADDED Requirements

### Requirement: A pushed workout is recorded as a logged session
The system SHALL take a workout received from the webhook and record it as an endurance session in the training records, matching how a manually-logged session is stored, so the auto path and the manual path converge on the same sources of truth.

#### Scenario: A workout arrives once and is logged
- **WHEN** the webhook receives a new, previously-unseen workout
- **THEN** the session is written to the Endurance record and the training log exactly once

#### Scenario: The same workout arrives again
- **WHEN** the webhook receives a workout it has already logged (same workout identity)
- **THEN** the session is NOT written a second time (no duplicate row or log entry)

### Requirement: Session written to the Training Records sheet
The system SHALL update the Google Sheet ('Training Records'): append the session to the **Endurance** tab and reflect its strict-Z2 minutes in the **Zone 2 Volume** section, using the same columns and live-formula conventions the manual log flow already maintains.

#### Scenario: Endurance row added
- **WHEN** a session is logged
- **THEN** a new Endurance-tab row appears with date, type, duration, distance, and average HR matching the received workout

#### Scenario: Strict-Z2 flows into the volume section
- **WHEN** a session with Zone-2 time is logged
- **THEN** the session's strict-Z2 minutes are included in the Zone 2 Volume per-day total and 7-day rolling calculation

### Requirement: Session appended to the training log
The system SHALL append the session to `walking_training.md` in the established table format, appending rather than overwriting, so the markdown stays the human-readable source of truth.

#### Scenario: Markdown row appended
- **WHEN** a session is logged
- **THEN** a new row is appended to `walking_training.md` using the report's stable schema and conventions, and prior rows are preserved

### Requirement: Report republished after a write
The system SHALL regenerate and deploy the endurance report (the `hung-report` app) after writing a session, so the live report reflects the newly-logged workout without any manual publish step.

#### Scenario: Report refreshes after logging
- **WHEN** a session is written to the sheet
- **THEN** the report is regenerated from the sheet and the change is deployed automatically

### Requirement: Deduplication by workout identity
The system SHALL remember which workouts it has already logged (keyed by the workout's unique identity) so that repeated triggers or repeated Zones-app opens never create duplicates.

#### Scenario: Duplicate push is ignored
- **WHEN** the same workout is pushed a second time after already being logged
- **THEN** the system skips the write and does not republish for that duplicate

### Requirement: Type, timezone and strict-Z2 conventions
The system SHALL record sessions per D K's logging conventions: map an Indoor walk to the **Treadmill** type (and an outdoor walk to **Real walk**), convert the workout's UTC time to **Europe/Berlin** for the record date, and derive **strict-Z2 minutes** from the workout's Zone 2 time (the 107–129 band).

#### Scenario: Indoor walk logged as Treadmill
- **WHEN** the received workout reports an Indoor location
- **THEN** the session type is recorded as "Treadmill" (not "Walking")

#### Scenario: Outdoor walk logged as real walk
- **WHEN** the received workout reports an outdoor location
- **THEN** the session type is recorded as "Real walk (outdoors)"

#### Scenario: Berlin date used
- **WHEN** the workout's start time is in UTC
- **THEN** the record's date is the workout's date in Europe/Berlin

#### Scenario: Strict-Z2 from the watch's per-minute Zone-2 time
- **WHEN** a workout payload carries `zones.zone2.time` (seconds actually spent in the 107–129 band)
- **THEN** that is the session's strict-Z2 minutes (sec ÷ 60), stored in col K and used by the Zone-2 Volume tab's formula — the true per-minute value, not an avg-HR estimate
- **AND** sessions without a watch value (manual/historical) keep the sheet's AVG-HR-in-band fallback so no row is lost