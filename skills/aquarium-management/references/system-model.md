# Live Peninsula system model

## Scope and authority

The live AAS system serves the Red Sea Peninsula aquarium and its
HYDROS-controlled mixing station.

| Domain | Authority |
| --- | --- |
| ReefControl, ReefRun, ReefMat, ReefDose, ReefPower | ReefBeat integration |
| ATO/AWC, HYDROS outputs, circulation schedules | HYDROS integration |
| Reservoir estimates and AAS calibration | Home Assistant helpers |
| Schedules, safety interlocks, water accounting | Home Assistant automations |
| Explanation and diagnosis | Hermes |

The 200XL is decommissioned from the live system. RSMAT-250 is removed.

## ReefControl water quality

Query the measurement and matching `level` together:

| Label | Measurement | Level |
| --- | --- | --- |
| Temperature | `sensor.aquarium_reefcontrol_pro_salinity_62a_temperature` | `sensor.aquarium_reefcontrol_pro_salinity_62a_temperature_level` |
| pH | `sensor.aquarium_reefcontrol_pro_ph_a4_ph` | `sensor.aquarium_reefcontrol_pro_ph_a4_level` |
| ORP | `sensor.aquarium_reefcontrol_pro_orp_1dc_orp` | `sensor.aquarium_reefcontrol_pro_orp_1dc_level` |
| Salinity | `sensor.aquarium_reefcontrol_pro_salinity_62a_salinity` | `sensor.aquarium_reefcontrol_pro_salinity_62a_level` |

Display salinity in PSU. Use only the salinity probe's temperature. Interpret
levels as `desired`, `acceptable`, `danger`, or `sensor_data_error`; do not
invent duplicate thresholds.

The leak detector is
`binary_sensor.rscontrolpro_415492340_leak_detector`: `on` means ReefBeat
reports all good; `off` is an alert. Ignore the separate leak value and level
sensors while they remain `unknown`.

Manually entered test-result helpers include alkalinity, calcium, magnesium,
nitrate, and phosphate under `input_number.peninsula_*`. They are measurements,
not target values.

## Water movement and life support

HYDROS circulation sensors:

- `sensor.aquarium_aquarium_hydros_left_xf350`
- `sensor.aquarium_aquarium_hydros_upper_octopulse4`
- `sensor.aquarium_aquarium_hydros_mid_octopulse4`
- `sensor.aquarium_aquarium_hydros_sump_octopulse_2`

Read each sensor's current attributes for its active schedule, pattern, and
direction. Do not infer a schedule from speed alone.

ReefRun critical entities:

- Return speed: `number.aquarium_rsrun_peninsula_pump_1_speed`
- Return state: `sensor.aquarium_rsrun_peninsula_pump_1_state`
- Skimmer speed: `number.aquarium_rsrun_peninsula_pump_2_speed`
- Skimmer state: `sensor.aquarium_rsrun_peninsula_pump_2_state`

ReefPower life-support map:

| Equipment | Switch | Consumption |
| --- | --- | --- |
| Helios Heater | `switch.aquarium_reefpower_s1` | `sensor.aquarium_reefpower_socket_1_consumption` |
| Sterilizer | `switch.aquarium_reefpower_s2` | `sensor.aquarium_reefpower_socket_2_consumption` |
| Ozone System | `switch.aquarium_reefpower_s3` | `sensor.aquarium_reefpower_socket_3_consumption` |
| CO2 Scrubber Bypass | `switch.aquarium_reefpower_s4` | `sensor.aquarium_reefpower_socket_4_consumption` |

For equipment state, compare the command switch, consumption, controller mode,
and any upstream safety condition. A switch state alone may not prove physical
operation.

## Dosing and filtration

The two ReefDose controllers use these entity prefixes:

- `aquarium_reefdose4_4_part_`
- `aquarium_reefdose4_extra_`

For a dosing question, discover and read the relevant head's supplement name,
configured daily dose, completed doses, dosed-today volume, schedule/config,
container volume or remaining estimate, calibration state, device mode, and
recent message. Never press manual-dose, fetch-config, calibration, tube
replacement, or delete-supplement buttons.

ReefMat 500 is the only live ReefMat. Read its state, usage, remaining-roll
estimate, device mode, messages, and maintenance entities as needed.

## HYDROS and auxiliary devices

HYDROS is monitoring-only in AAS. Common diagnostic states include AWC, ATO,
Sump Level ATO, Sump Too High, Make Salt, RODI Low, TDS, water production,
Feed Stir, Fresh Stir, and other intermittent outputs. Entity IDs can change
with HYDROS naming; verify the current registry mapping before using them.

The TP-Link Other Devices group includes phyto air, phyto, cabinet lights,
power check, and the tablet outlet. These are controllable in Home Assistant,
but Hermes v1 only reports their state.
