# Maintenance and safety

## ReefBeat quick actions

ReefBeat Cloud exposes the configured actions as switches:

| Dashboard label | Entity |
| --- | --- |
| Return Maintenance | `switch.aquarium_peninsula_maintenance` |
| Skimmer Maintenance | `switch.aquarium_peninsula_skimmer_maintenance` |
| Doser Maintenance | `switch.aquarium_peninsula_doser_maintenance_2` |
| Emergency | `switch.aquarium_peninsula_emergency` |

ReefBeat owns device membership and delayed returns. Do not duplicate its
timing in Hermes or assume Emergency controls HYDROS ATO/AWC equipment.

Hermes v1 may report whether an action is active and explain its expected
scope. It must not toggle an action.

## Device maintenance

ReefBeat is authoritative for ReefRun, ReefDose, ReefMat, ReefPower, and
ReefControl maintenance intervals, completion dates, and supported
calibrations. Read integration entities and the output of
`automation.aquarium_reefbeat_watch`; do not create parallel maintenance facts.

Unknown button states are normal for stateless Home Assistant buttons and do
not prove a maintenance problem.

## Sterilizer safety

`automation.aquarium_sterilizer_return_safety` protects
`switch.aquarium_reefpower_s2`.

- Turn the Sterilizer off immediately when the ReefRun return state is not
  `operational` or return speed is below 15%.
- Restart only after both conditions remain safe continuously for three
  minutes, followed by a recheck.
- A manual start during an unsafe condition is overridden by the automation.

When explaining a stopped Sterilizer, cite the return state, return speed,
automation state or trace if available, Sterilizer switch, and consumption.

## Ozone schedule

`automation.aquarium_ozone_system_overnight_schedule` normally runs
`switch.aquarium_reefpower_s3` from 23:00 through 07:00. A manual dashboard
toggle is reasserted at the next schedule boundary and after Home Assistant
starts. Verify local time, automation state, switch state, and consumption.

## CO2 scrubber bypass

`switch.aquarium_reefpower_s4` drives a 12 V bypass valve:

- switch off: bypass closed, `CO2 Scrubbing On`
- switch on: bypass open, `CO2 Bypass Open`

While ReefBeat Socket 4 is in sensor mode, this is status-only. Do not suggest
a toggle unless the live controller mode has first been verified as compatible
with Home Assistant control.

## Control boundary

For any requested control action, identify the appropriate Home Assistant,
ReefBeat, or HYDROS surface and explain the current safety context. Do not
execute it in v1. Never recommend bypassing an interlock merely to restore an
expected dashboard state.
