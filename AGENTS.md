# AGENTS.md

# Aquarium Automation Suite (AAS)

## Project

This repository contains the Home Assistant configuration for a multi-aquarium automation system.

The primary objective is to create a maintainable, modular, production-quality Home Assistant environment centered around aquarium automation.

The first subsystem is the Aquarium Water Management Suite (AWMS).

---

# Aquarium Configuration

There are two independent aquariums.

## 1. Peninsula

Primary display aquarium.

Equipment includes:

- Automatic Top Off (ATO)
- Automatic Water Change (AWC)
- Hydros Control
- Red Sea ReefDose 4
- Hydros Minnow
- Hydros Simple Doser
- RODI reservoir
- Saltwater reservoir

RODI and Salt reservoirs are tracked by Home Assistant.

Inventory is stored in milliliters.

The dashboard displays gallons.

---

## 2. Red Sea 200XL

Separate aquarium.

Equipment includes:

- Independent ATO
- Manual water changes
- Separate ATO reservoir
- Red Sea ReefDose 4

The 200xl ATO reservoir is manually filled from the Peninsula RODI reservoir.

Current operating fill level is approximately 25 gallons.

Physical maximum capacity is approximately 40 gallons.

AWMS normal operating fill is approximately 25 gallons / 94635 mL. Physical capacity is approximately 40 gallons.

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

Normally filled to approximately 25 gallons.

Water is manually transferred from Peninsula RODI.

AWMS normally targets about 94635 mL, roughly 25 gallons, even though the physical reservoir can hold more.

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
