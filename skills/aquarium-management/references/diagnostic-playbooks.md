# Diagnostic playbooks

## Whole-system status

Query only enough state to establish:

1. ReefControl measurements and levels, plus leak health.
2. Return and skimmer states/speeds.
3. Active ReefBeat quick actions and abnormal integration messages.
4. HYDROS ATO/AWC and alert-condition sensors.
5. Sterilizer, heater, ozone, and scrubber-bypass states when relevant.
6. ReefMat, ReefDose, and reservoir items that are low, overdue, abnormal, or
   unavailable.

Lead with exceptions. Do not enumerate every normal outlet.

## Explain an alert

1. Read the alerting entity and its supporting measurement or level.
2. Check whether a maintenance or emergency quick action explains the state.
3. Check integration availability and recent device messages.
4. Check related equipment or process state.
5. State whether the alert is confirmed, suppressed by an operating rule, or
   inconclusive because data is missing.

Treat these HYDROS conditions as alert-oriented:

- Sump ATO: alert on Dry or Overflow, except Dry during an active AWC process.
- Sump Too High: alert when Wet.
- Make Salt: alert when Dry.
- RODI Low: alert when Dry.
- TDS: alert above 0; check both available TDS stages if the installation
  exposes two values.

Verify the integration's actual state vocabulary before applying these rules.

## Diagnose water quality

Read each ReefControl measurement with its `level`. Use the integration's level
as the primary severity and quote the measurement for context. If the level is
`sensor_data_error`, diagnose sensor/data quality rather than aquarium
chemistry. If the measurement and level disagree visibly, report the mismatch
and recent update timing instead of inventing a threshold.

For weekly test helpers, describe trends only if history is available. Do not
compare against a target that has not been modeled in Home Assistant.

## Diagnose stopped or unexpected equipment

1. Read the device switch/state and consumption.
2. Read its controller mode and recent messages if exposed.
3. Check maintenance and Emergency quick actions.
4. Check the equipment-specific schedule or safety interlock.
5. Distinguish commanded state, measured power, and inferred physical state.

For the Sterilizer, always correlate return state and speed. For Ozone, check
the overnight schedule. For CO2 scrubbing, remember the switch is electrically
inverted from scrubbing state.

## Diagnose a dosing discrepancy

For the named head, query live ReefDose configuration and status before
reasoning:

- supplement name and head number
- configured daily dose and schedule
- completed versus planned doses
- dosed-today volume
- container volume or remaining estimate
- calibration and maintenance state
- device mode, availability, and recent messages

Separate a missed scheduled dose from a stale container estimate, calibration
issue, paused maintenance state, or unavailable integration. Never infer the
configured dose from an old dashboard screenshot or reference value.

## Handle missing data

`unknown` or `unavailable` means the answer is incomplete. Check a related
connectivity entity, controller mode, and last update. Do not call a device
failed solely because an optional configuration entity is unavailable. State
which conclusion cannot be verified.
