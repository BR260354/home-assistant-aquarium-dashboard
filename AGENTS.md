# AGENTS.md

# Aquarium Automation Suite (AAS)

## Project

This repository contains the Home Assistant configuration for a multi-aquarium automation system.

The primary objective is to create a maintainable, modular, production-quality Home Assistant environment centered around aquarium automation.

The first subsystem is the Aquarium Water Management Suite (AWMS).

---

# Aquarium Configuration

The live AAS dashboards now serve the Peninsula. The former 200XL configuration
is retained only for decommissioning and handoff until its hardware is fully
removed from this Home Assistant instance.

## 1. Peninsula

Primary display aquarium.

Equipment includes:

- Automatic Top Off (ATO)
- Automatic Water Change (AWC)
- Hydros Control
- Red Sea ReefDose 4 (`ReefDose4 - 4-Part`) for Red Sea Complete 4-Part
- Red Sea ReefDose 4 (`ReefDose4-Extra`) for supplemental dosing
- RODI reservoir
- Saltwater reservoir

RODI and Salt reservoirs are tracked by Home Assistant.

Inventory is stored in milliliters.

The dashboard displays gallons.

Peninsula water-quality monitoring is provided by Red Sea ReefControl Pro via
ReefBeat. The former Hydros temperature, pH, ORP, and salinity entities have
been removed and must not be used in new dashboard work. Current entities are:

- Temperature: `sensor.aquarium_reefcontrol_pro_salinity_62a_temperature`
- Temperature level: `sensor.aquarium_reefcontrol_pro_salinity_62a_temperature_level`
- pH: `sensor.aquarium_reefcontrol_pro_ph_a4_ph`
- pH level: `sensor.aquarium_reefcontrol_pro_ph_a4_level`
- ORP: `sensor.aquarium_reefcontrol_pro_orp_1dc_orp`
- ORP level: `sensor.aquarium_reefcontrol_pro_orp_1dc_level`
- Salinity: `sensor.aquarium_reefcontrol_pro_salinity_62a_salinity`
- Salinity level: `sensor.aquarium_reefcontrol_pro_salinity_62a_level`
- Leak detector: `binary_sensor.rscontrolpro_415492340_leak_detector`

Use only the salinity probe's temperature reading. Display salinity in PSU.
Keep the visible dashboard labels concise: Temperature, pH, ORP, and Salinity.
Use the ReefControl `level` entities (`desired`, `acceptable`, `danger`, or
`sensor_data_error`) for indicators and alerts instead of duplicating Red Sea
thresholds. The leak detector is `on` when ReefBeat reports all good and should
alert only when it is `off`. The separate leak value and level sensors currently
report `unknown` and should remain hidden until they provide usable states.

The Hydros Simple Doser has been removed from the Peninsula. Its Home Assistant
entities are disabled and should not be used in new dashboard or automation
work.

The Peninsula OctoPulse 2 has returned and now runs in the sump. Current
circulation devices and entity IDs are:

- Mid OctoPulse4: `sensor.aquarium_aquarium_hydros_mid_octopulse4`
- Upper OctoPulse4: `sensor.aquarium_aquarium_hydros_upper_octopulse4`
- Left XF350: `sensor.aquarium_aquarium_hydros_left_xf350`
- Sump OctoPulse 2: `sensor.aquarium_aquarium_hydros_sump_octopulse_2`

The former Hydros Sump Stir output has been removed and should not be used in
new dashboard or automation work.

The live Hydros Outputs dashboard controls are select entities with `Auto`,
`On`, and `Off` options:

- Water Feed: `select.aquarium_aquarium_hydros_water`
- Fresh Stir: `select.aquarium_aquarium_hydros_fresh_stir`
- Feed Stir: `select.aquarium_aquarium_hydros_feed_stir`
- AWC ATO: `select.aquarium_aquarium_hydros_awc`
- AWC Drain: `select.aquarium_aquarium_hydros_awc_drain`
- AWC Fill: `select.aquarium_aquarium_hydros_awc_fill`
- Avast Feed: `select.aquarium_aquarium_hydros_avast_feed`
- Display Fans: `select.aquarium_aquarium_hydros_display_fans`

Use the corresponding `binary_sensor.aquarium_aquarium_hydros_<output>_running`
entity for live status where available. AWC Drain and AWC Fill use
`input_boolean.peninsula_awc_drain_running` and
`input_boolean.peninsula_awc_fill_running`, updated by their AWC event
automations. Dashboard status text should distinguish `Running`, a manually
selected `On`/`Off`, and `Auto · Idle`. Keep the output and its mode controls
visually grouped. Mobile keeps Hydros Outputs inside an expander; Tablet shows
the controls in a full-width cyan-accented card with responsive columns.

Both ReefDose 4 controllers and all eight dosing-head devices belong to the
Home Assistant `Aquarium` area.

ReefDose entity ID prefixes:

- `aquarium_reefdose4_4_part_`
- `aquarium_reefdose4_extra_`

Peninsula ReefPower outlets:

- Helios Heater switch: `switch.aquarium_reefpower_s1`
- Helios Heater power: `sensor.aquarium_reefpower_socket_1_consumption`
- Sterilizer switch: `switch.aquarium_reefpower_s2`
- Sterilizer power: `sensor.aquarium_reefpower_socket_2_consumption`
- Ozone System switch: `switch.aquarium_reefpower_s3`
- Ozone System power: `sensor.aquarium_reefpower_socket_3_consumption`
- CO2 Scrubber Bypass switch: `switch.aquarium_reefpower_s4`
- CO2 Scrubber Bypass power: `sensor.aquarium_reefpower_socket_4_consumption`

The CO2 Scrubber Bypass controls a 12 V ball valve in the skimmer air path.
The electrical state is intentionally inverted from the scrubbing state:

- Switch off: bypass closed; skimmer air passes through the CO2 scrubber.
- Switch on: bypass open; skimmer air bypasses the CO2 scrubber.

Use `CO₂ Scrubber Bypass` for the control. Keep status text compact: display
`CO₂ Scrubbing On` when the switch is off, and `CO₂ Bypass Open` when the
switch is on. Do not use the retired Hydros Not Scrubbing entity in new
dashboard work.

While ReefPower Socket 4 is in ReefBeat `sensor` mode, dashboard cards must be
status-only because ReefBeat owns the output state. Do not provide a normal
tap-to-toggle action. If control is later moved to Home Assistant, first place
the socket in an HA-compatible `off`/`on` mode and verify the mode/select
commands before adding automation or dashboard controls.

The Sterilizer is protected by
`automation.aquarium_sterilizer_return_safety`. It turns off immediately when
`sensor.aquarium_rsrun_peninsula_pump_1_state` is not `operational` or
`number.aquarium_rsrun_peninsula_pump_1_speed` is below 15%. After both return
conditions are safe again, the automation waits three continuous minutes,
rechecks both conditions, and then restarts the Sterilizer. The Systems card is
a manual toggle, but an unsafe manual start is overridden by the automation.

The Ozone System combines the former Hydros Ozotech, Dryer, and Ozone Air
outputs. Do not use those retired Hydros entities in new dashboard or
automation work. The Ozone System normally runs overnight from 23:00 through
07:00 via `automation.aquarium_ozone_system_overnight_schedule`. Dashboard
cards may toggle the ReefPower switch manually; the automation reasserts the
schedule at the next boundary and after Home Assistant starts.

ReefBeat owns Red Sea device state, configuration, and supported maintenance
intervals/dates. Do not recreate those maintenance records with parallel AAS
helpers. `automation.aquarium_reefbeat_watch` uses the integration's official
blueprint for Red Sea alerts and maintenance reminders. ReefBeat does not own
or control the Peninsula ATO/AWC process; that remains HYDROS-driven.

Current ReefBeat Cloud quick-action entities are:

- Return Maintenance: `switch.aquarium_peninsula_maintenance`
- Skimmer Maintenance: `switch.aquarium_peninsula_skimmer_maintenance`
- Doser Maintenance: `switch.aquarium_peninsula_doser_maintenance_2`
- Emergency: `switch.aquarium_peninsula_emergency`

The earlier entities without the `aquarium_` prefix, plus
`switch.aquarium_peninsula_doser_maintenance`, are retired restored entities
and report `unavailable`. Do not use them in dashboards or automations.

---

## 2. Red Sea 200XL

Decommissioned from the live AAS dashboards and being prepared for handoff.
The files under `handoff/` are the reusable package for the receiving Home
Assistant installation.

RSMAT-250 has been removed from the current ReefBeat integration. Any remaining
RSMAT-250 entity reference is historical or handoff material and must not be
introduced into live Peninsula automations or dashboards.

Equipment includes:

- Independent ATO
- Manual water changes
- Separate ATO reservoir
- Hydros Minnow

The Hydros Minnow is installed on the 200xl. Its exposed entities belong to the
Home Assistant `Bedroom` area:

- `binary_sensor.bedroom_aquarium_minnow_head_1`
- `sensor.bedroom_aquarium_minnow_head_1_dosed_today`
- `sensor.bedroom_aquarium_minnow_head_1_reservoir_remaining`

The Minnow's second physical head is unused and is not currently exposed by
the Hydros integration. The 200xl Dosing expander represents both heads: Head
1 uses live entities and Head 2 is labeled not used / not configured.

The Minnow entities have explicit `Bedroom` area overrides, but the Hydros
integration currently publishes them under its `Aquarium Hydros` parent
device. Do not move that parent device to Bedroom because it also owns the
Peninsula entities. The parent association can change only after the Minnow
output is assigned to the Bedroom Aquarium collective in Hydros and the
integration is reloaded.

The 200xl no longer has a ReefDose controller. Its former ReefDose 4 is now
`ReefDose4-Extra` on the Peninsula.

The 200xl ATO reservoir is manually filled from the Peninsula RODI reservoir.

The normal maximum fill is approximately 20 gallons.

Physical maximum capacity is approximately 30 gallons.

AWMS normal operating maximum is approximately 20 gallons / 75708 mL. Physical capacity is approximately 30 gallons / 113562 mL.

Current estimated inventory is approximately 17 gallons / 64352 mL.

Pump rate:

280 mL/minute.

Future versions will automatically deduct inventory based on ATO runtime.

Do not manually remove inventory from the 200xl ATO reservoir in AWMS.

When filling the 200xl ATO reservoir, AWMS deducts Peninsula RODI and increases `input_number.awms_200xl_ato_remaining_ml`.

Future Hydros runtime tracking will automatically deduct 200xl ATO usage.

Hydros entity:

binary_sensor.bedroom_aquarium_ato

---

# Water Inventory

Reservoirs

## Peninsula RODI

Capacity

111700 mL

29.5 gallons

Automatically filled by Hydros.

Hydros binary sensor:

sensor.aquarium_hydros_water

When filling stops (high sensor reached), inventory resets to 111700 mL.

---

## Peninsula Salt

Capacity

111700 mL

Filled manually from the Peninsula RODI reservoir.

Used for:

- Automatic Water Changes
- Manual water changes
- Future expansion

---

## 200xl ATO Reservoir

Normally filled to no more than approximately 20 gallons.

Water is manually transferred from Peninsula RODI.

AWMS uses an operating maximum of 75708 mL, roughly 20 gallons, even though the physical reservoir can hold about 30 gallons / 113562 mL.

Current estimated inventory is approximately 64352 mL / 17 gallons.

---

# Inventory Rules

Inventory is ALWAYS stored internally in milliliters.

Dashboard displays gallons.

Never store gallons internally.

Conversion:

1 gallon = 3785.41 mL

---

# Existing Inventory Helpers

RODI

input_number.awms_rodi_remaining_ml

Salt

input_number.awms_salt_remaining_ml

200xl ATO

input_number.awms_200xl_ato_remaining_ml

---

# Authoritative AAS Configuration Helpers

These helpers are created through the Home Assistant UI and are the live
calibration/configuration source for HYDROS-driven AWMS calculations:

- `input_number.peninsula_ato_pump_rate_ml_min` — initial value 400.2 mL/min
- `input_number.peninsula_awc_fill_pump_rate_ml_min` — initial value 400.2 mL/min
- `input_number.peninsula_rodi_capacity_ml` — initial value 111700 mL
- `input_number.peninsula_salt_capacity_ml` — initial value 111700 mL

Automations must read these helpers rather than hard-code the calibration or
capacity. The initial pump rate preserves the previous 6.67 mL/second behavior.
Inventory and capacity remain stored in milliliters.

---

# AWMS Helpers

Created through the Home Assistant UI.

Do NOT define helpers in YAML.

Current helpers include:

input_number.awms_adjustment_amount

input_select.awms_adjustment_unit

input_select.awms_adjustment_action

input_select.awms_source_reservoir

input_select.awms_workflow

Amount unit options:

- mL
- L
- oz
- gal

Action options:

- Remove
- Transfer

Reservoir options:

- RODI
- Salt

Typical used-for options:

- Plants
- Make New Salt
- 200xl ATO
- 200xl Water Change
- Other

---

# Dashboard Standards

Use:

- custom:expander-card
- Mushroom cards
- Mushroom chips
- card_mod
- Gauge Card Pro

Avoid standard Entity cards whenever possible.

Maintain visual consistency with the existing aquarium dashboard.

---

# Gauge Cards

Existing Gauge Card Pro cards are NOT to be replaced.

AWMS should enhance them, not redesign them.

Eventually calculations should move into template sensors to reduce duplicated Jinja.

---

# UI Philosophy

Dashboard first.

Backend second.

The dashboard is the product.

Scripts and templates exist only to support the dashboard.

---

# Water Management Workflow

Supported operations:

Remove

Transfer

AWMS apply script:

script.awms_apply_adjustment

Implemented through the Home Assistant Script UI.

Do NOT define it in scripts.yaml.

Future:

Automatic inventory updates.

---

# Common Workflows

Plants

Always RODI.

Make New Salt

Uses RODI to fill Salt reservoir.

200xl ATO

Uses RODI only.

Deducts Peninsula RODI and increases `input_number.awms_200xl_ato_remaining_ml`.

200xl Water Change

Uses Salt reservoir.

Other

User chooses reservoir.

---

# Coding Standards

Prefer:

if / then

instead of nested choose blocks.

Use:

is_state()

instead of comparing variables.

This is especially important in scripts because Home Assistant 2026.7.1 does not reliably expose Variables actions inside Choose template conditions.

Do NOT use Script UI Variables actions for AWMS amount conversion.

Convert `input_number.awms_adjustment_amount` and `input_select.awms_adjustment_unit` inline inside each service value template instead.

Keep scripts small.

One responsibility per logical block.

Extensively comment YAML.

---

# Home Assistant Version

Target version:

2026.7.1

Use current Script syntax.

Scripts are created via the UI.

Helpers are created via the UI.

Templates remain in template.yaml.

Automations remain in automations.yaml unless there is a compelling reason otherwise.

---

# Future Roadmap

Version 1

Inventory

Dashboard

Manual Adjustments

Transfers

Current completed AWMS work:

- Dashboard framework
- Helpers
- Reservoir gauges
- Water Management expander
- Apply script
- Remove logic
- Transfer logic

Current in-progress AWMS work:

- Preview card redesign
- Interactive chips

Planned dashboard sections:

- Reservoir Status
- Quick Actions
- Activity
- Statistics

Water Management is located under:

Maintenance

↓

Water Management

Quick Actions current layout:

- Action
- Used For dropdown
- Reservoir chips for Other only
- Amount
- Preview
- Apply

Color conventions:

- RODI: Blue
- Salt: Purple
- 200XL: Green
- Remove: Red
- Transfer: Blue

Version 1.1

Activity Log

Statistics

Automatic Hydros refill handling

Version 1.2

200XL automation

Runtime tracking

Usage analytics

Version 2

Forecasting

Maintenance planning

Consumables

Predictive reservoir depletion

---

# Development Philosophy

This project should feel like a commercial aquarium controller rather than a collection of Home Assistant cards.

Priorities:

1. Reliability
2. Readability
3. Maintainability
4. Consistent UI
5. Minimal duplicated logic
6. Production-quality YAML
