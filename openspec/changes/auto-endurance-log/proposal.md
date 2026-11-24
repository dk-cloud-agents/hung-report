# Proposal

## Why

D K currently logs every endurance session by screenshotting it (Zones app) and sending it to Hermes in chat — manual, easy for OCR to misread, and the screenshot loses the zone-band data. Spike **SCRUM-9** proved a fully-free, reliable alternative end-to-end: a workout recorded on the watch is pushed automatically when the Zones app is opened, and the server already parses it (type, duration, distance, calories, HR, zones, strict-Z2). What is missing is the last link — turning that parsed workout into a logged row in her training records and a refreshed report, with no copy-paste. This change builds that link (the "write-path").

## The UX flow we designed (what D K experiences)

1. **She does the workout** — her Apple Watch records minutes + heart rate (Apple Workout app or Zones).
2. **She ends it** on the watch.
3. **She opens the Zones app on her phone** — that one opening is the trigger. (The Apple Watch alone does NOT trigger it; the phone app-open does — spike-verified.)
4. The automation she built (`When "Zones" is opened` → *Export Workout as JSON* → *Get contents of URL*) posts the workout JSON to the VPS webhook.
5. The server parses it: workout type, start/end, duration, distance, calories, avg/max HR, per-zone time, and **strict-Z2 minutes** (from Zones' Zone 2 time, D K's 107–129 band).
6. **NEW (this change):** the server writes the session into her Training Records sheet (**Endurance** tab + **Zone 2 Volume** section) and appends it to `walking_training.md`, **skip-if-already-logged**, then **publishes the refreshed report**.
7. Done — no screenshot, no typing, no copy-paste. The report reflects the walk.

**Net UX:** *"walk → end → open Zones once → it's logged and the report is fresh."*

## What Changes

- **New capability `endurance-auto-log`:** the webhook's parsed workout is written into the real training records (sheet + markdown) and the report is republished — the write-path.
- Reuses the existing pipeline: the sheet sync + `publish_report.py` already know how to write Endurance/Zone 2 Volume and deploy the app; this links the webhook to them.
- Dedup so repeated Zones-app opens never double-log a session.
- Session types mapped to her log conventions (Indoor walk → Treadmill; else Real walk) and times converted from UTC to Europe/Berlin date.
- **BREAKING** none — the report UI/design is unchanged; it only gets new data automatically.

## Capabilities

### New Capabilities
- `endurance-auto-log`: turning a pushed workout into a logged training record (sheet + markdown) and a republished report, deduplicated and convention-correct.

### Modified Capabilities
(none — the report's existing capabilities are unchanged)

## Impact

- `receiver.py` (the spike webhook) grows a write step that calls the existing sheet-sync + publish logic.
- Touches `walking_training.md` (append) and the Training Records sheet (Endurance + Zone 2 Volume) — the same files/logic the manual log flow already maintains, so no schema change.
- Uses the existing alert helper on write/publish failure (silent-failure rule).
- JIRA tracking: **SCRUM-10** (Story, label `hungs-fitness-report`); flow per SCRUM-7 workflow.