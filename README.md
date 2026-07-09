# Home Assistant Aquarium Dashboard

Maintained by [BR260354](https://github.com/BR260354).

Public GitHub Pages draft: [`docs/index.md`](docs/index.md)

Helper/entity rename migration plan: [`docs/helper-rename-plan.md`](docs/helper-rename-plan.md)

## Overview

Home Assistant Aquarium Dashboard is a dashboard-centered Home Assistant configuration for monitoring and managing multiple reef aquariums from a single place.

The project focuses on water inventory, RODI/Salt management, aquarium status monitoring, and a few practical water-movement automations. It is built using Home Assistant, Hydros controllers, Red Sea ReefBeat devices, Mushroom cards, Gauge Card Pro, and custom Lovelace dashboards.

The primary subsystem is the **Aquarium Water Management Suite (AWMS)**.

---

# Screenshots

## Peninsula

![Peninsula water parameters and status chips](docs/assets/screenshots/dashboard-01.png)

## 200XL

![200XL water parameters and status chips](docs/assets/screenshots/dashboard-02.png)

## Water Management

![Maintenance tab with reservoir gauges and AWMS quick actions](docs/assets/screenshots/dashboard-03.png)

![AWMS Make New Salt preview and apply controls](docs/assets/screenshots/dashboard-04.png)

## Equipment Sections

![Water flow section with pump and flow gauges](docs/assets/screenshots/dashboard-05.png)

![Dosing section with Red Sea and Hydros dosing gauges](docs/assets/screenshots/dashboard-06.png)

![Filtration and general equipment section with usage graph](docs/assets/screenshots/dashboard-07.png)

---

# Objectives

- Maintain accurate water inventory
- Support practical water-management workflows
- Provide an intuitive dashboard
- Keep Home Assistant configuration modular
- Minimize duplicated logic
- Keep the configuration understandable for others who want dashboard ideas

---

# Aquarium Systems

## Peninsula Reef

Primary display aquarium.

### Equipment

- Hydros Control
- Automatic Top Off (ATO)
- Automatic Water Change (AWC)
- Red Sea ReefDose 4
- Hydros Minnow
- Hydros Simple Doser
- RODI reservoir
- Saltwater reservoir

### Reservoir Capacities

| Reservoir | Capacity |
|-----------|---------:|
| RODI | 111,700 mL (29.5 gal) |
| Salt | 111,700 mL (29.5 gal) |

Hydros automatically refills the RODI reservoir.

---

## Red Sea 200XL

Independent aquarium.

### Equipment

- Independent ATO
- Manual water changes
- Separate ATO reservoir
- Red Sea ReefDose 4

Current operating fill:

Approximately **25 gallons**

Maximum reservoir:

Approximately **40 gallons** physical maximum.

AWMS operating cap:

Approximately **25 gallons** / **94,635 mL**

ATO pump:

**280 mL/minute**

The 200xl ATO reservoir is manually filled from the Peninsula RODI reservoir.

Current AWMS behavior deducts Peninsula RODI and increases
`input_number.awms_200xl_ato_remaining_ml` when manually filling the
200xl ATO reservoir.

---

# Water Flow

```
RODI Source
      │
      ▼
Peninsula RODI Reservoir
      │
      ├──────────────► Peninsula ATO
      │
      ├──────────────► Make New Salt
      │
      ├──────────────► Plants
      │
      └──────────────► 200xl ATO Reservoir
                              │
                              ▼
                        200XL Aquarium
```

---

# Inventory Model

All inventory is stored internally in **milliliters**.

The dashboard displays **gallons**.

Conversion:

```
1 gallon = 3785.41 mL
```

No inventory calculations should ever be performed using gallons internally.

---

# Home Assistant Structure

Helpers are created through the Home Assistant UI.

Scripts are created through the Script UI.

Templates remain in:

```
template.yaml
```

Automations remain in:

```
automations.yaml
```

AWMS workflow defaults are managed by an automation snippet:

```
snippets/awms-sync-workflow-defaults-automation.yaml
```

Dashboard YAML is managed through Lovelace.

---

# Existing Helpers

## Reservoir Inventory

```
input_number.awms_rodi_remaining_ml
```

Peninsula RODI inventory

```
input_number.awms_salt_remaining_ml
```

Peninsula Salt inventory

```
input_number.awms_200xl_ato_remaining_ml
```

200xl ATO inventory

---

## AWMS Helpers

```
input_number.awms_adjustment_amount
```

Adjustment amount entered by the user.

Do not rely on this helper's Home Assistant unit of measurement for AWMS math
or display. The selected AWMS unit helper is authoritative.

The selected unit comes from:

```
input_select.awms_adjustment_unit
```

Options

- mL
- L
- oz
- gal

```
input_select.awms_adjustment_action
```

Options

- Remove
- Transfer

```
input_select.awms_source_reservoir
```

Options

- RODI
- Salt

```
input_select.awms_workflow
```

Examples

- Plants
- Make New Salt
- 200xl ATO
- 200xl Water Change
- Other

Remove old `Cleaning`, `Testing`, and `200xl ATO Fill` options from this helper.
Use `200xl ATO` for the bedroom ATO reservoir workflow.
Rename any old `Salt Mixing` option to `Make New Salt`.

---

# Dashboard

The Water Management dashboard is located under

```
Maintenance
    ▼ Water Management
```

Current layout

```
Reservoir Status

    RODI Gauge
    Salt Gauge

▼ Quick Actions

    Action

    Used For dropdown

    Reservoir chips (Other only)

    Amount

    Preview

    Apply
```

# Existing Gauge Cards

Gauge Card Pro cards already exist for:

- RODI
- Salt

These gauges are considered production components.

Future work should enhance them rather than replace them.

Formatting logic will gradually move into template sensors to eliminate duplicated Jinja.

---

# Current Script

```
script.awms_apply_adjustment
```

Functions

- Remove water
- Transfer

The script uses

```
if / then
```

instead of nested Choose blocks.

It also uses

```
is_state()
```

for all helper comparisons.

The script converts `input_number.awms_adjustment_amount` to milliliters using `input_select.awms_adjustment_unit` inline inside each service value template.

Do not use a Script UI Variables action for this conversion.

This approach is required for Home Assistant 2026.7.1 due to variable scope behavior in Script UI.

---

# Dashboard Philosophy

The dashboard is the product.

Scripts, templates, and automations exist only to support the dashboard.

Avoid exposing raw helpers whenever possible.

Prefer Mushroom cards over standard Entity cards.

Maintain consistent spacing, colors, and typography.

---

# Color Standards

| Item | Color |
|------|-------|
| RODI | Blue |
| Salt | Purple |
| 200XL | Green |
| Remove | Red |
| Transfer | Blue |
| Preview | Yellow |
| Apply | Cyan |

---

# Typical Workflows

## Plants

Reservoir

RODI

Action

Remove

---

## Make New Salt

Make New Salt fills the Salt reservoir to full from Peninsula RODI.

AWMS calculates how much Salt capacity is empty, confirms Peninsula RODI has
enough water, deducts that amount from RODI, and sets Salt to full.

The dashboard hides the manual Adjust Amount control for Make New Salt and
shows the calculated transfer amount instead.

Transfer

RODI

↓

Salt

---

## 200xl ATO

Reservoir

RODI source

Action

Remove

Effect

RODI decreases

↓

200xl ATO remaining increases

---

## 200xl Water Change

Reservoir

Salt

Action

Remove

---

## Activity Log

Successful AWMS adjustments write to the Home Assistant Logbook under the
reservoir helper that changed.

Optional dashboard card:

```
snippets/awms-recent-activity-card.yaml
```

The first activity view shows the last seven days of RODI, Salt, and 200xl ATO
inventory changes.

---

# Coding Standards

Prefer

```
if / then
```

instead of deeply nested Choose blocks.

Use

```
is_state()
```

instead of comparing script variables.

Store inventory only in mL.

Display gallons only in the UI.

Comment YAML generously.

Organize files into logical sections.

Avoid duplicated calculations.

---

# Acknowledgements

This project has been designed collaboratively with ChatGPT over multiple design and implementation sessions.

This repository is shared as a reference for other aquarium and Home Assistant hobbyists who may want ideas for their own dashboards.
