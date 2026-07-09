# Home Assistant Helper Rename Plan

This is a migration checklist for standardizing helper/entity names before publishing the Aquarium Automation Suite publicly.

The goal is to make the published YAML easy for other Home Assistant users to understand while avoiding broken references in dashboards, scripts, automations, and templates.

## Recommendation

Do not rename everything at once.

Rename in phases:

1. Manual testing helpers
2. Maintenance date helpers
3. AWMS inventory helpers
4. AWMS workflow/control helpers
5. Template sensors that depend on renamed helpers

The AWMS helpers are the riskiest because the apply script, preview card, workflow automation, gauges, and activity card all reference them.

## Proposed Naming Standard

Use this format:

```text
<domain>.<system>_<thing>_<measurement_or_role>_<unit_if_stored_value>
```

Examples:

```text
input_number.peninsula_alkalinity_dkh
input_number.200xl_nitrate_no3_ppm
input_number.awms_rodi_remaining_ml
input_datetime.peninsula_saltwater_batch_last_made
sensor.awms_rodi_remaining_percent
```

Rules:

- Use `peninsula` for the primary display aquarium.
- Use `200xl` for the smaller Red Sea 200XL aquarium.
- Use `awms` for shared water-management helpers.
- Include units in helper IDs when the stored value depends on units.
- Keep internal inventory helpers in milliliters with `_ml`.
- Avoid unclear shorthand like `200g2`; use `200xl` because it matches the aquarium name.
- Avoid ambiguous local names like `bedroom_ato` in public examples unless the room name matters.

## Rename Map

| Current entity | Suggested public entity | Priority | Status | Notes |
| --- | --- | --- | --- | --- |
| `input_number.alkalinity_dkh` | `input_number.peninsula_alkalinity_dkh` | Medium | Done | Manual test value |
| `input_number.nitrate_no3` | `input_number.peninsula_nitrate_no3_ppm` | Medium | Done | Manual test value |
| `input_number.phosphates_po4` | `input_number.peninsula_phosphate_po4_ppm` | Medium | Done | Manual test value |
| `input_number.calcium_ca` | `input_number.peninsula_calcium_ca_ppm` | Medium | Done | Manual test value |
| `input_number.magnesium_mg` | `input_number.peninsula_magnesium_mg_ppm` | Medium | Done | Manual test value |
| `input_number.200g2_alkalinity_dkh` | `input_number.200xl_alkalinity_dkh` | Medium | Done | Manual test value |
| `input_number.200g2_nitrate_no3` | `input_number.200xl_nitrate_no3_ppm` | Medium | Done | Manual test value |
| `input_number.200g2_phosphates_po4` | `input_number.200xl_phosphate_po4_ppm` | Medium | Done | Manual test value |
| `input_datetime.water_change` | `input_datetime.peninsula_saltwater_batch_last_made` | Low | Done | Current label says "New Saltwater Batch Last Made" | I renamed to Peninsula Salt Water Last Made in the helper
| `input_datetime.bioclean` | `input_datetime.peninsula_bioclean_last_dosed` | Low | Done | Maintenance tracking |
| `input_datetime.ato_start_time` | `input_datetime.awms_rodi_last_filled_at` | High | Done | Used by reservoir gauge |
| `input_datetime.awc_fill_start_time` | `input_datetime.awms_salt_last_filled_at` | High | Done | Used by reservoir gauge |
| `input_datetime.200xl_ato_start_time` | `input_datetime.200xl_ato_started_at` | High | Done | ATO runtime start timestamp |
| `input_number.ato_estimated_remaining_ml` | `input_number.awms_rodi_remaining_ml` | High | Done | Core AWMS inventory |
| `input_number.fill_estimated_remaining_ml` | `input_number.awms_salt_remaining_ml` | High | Done | Core AWMS inventory |
| `input_number.bedroom_ato_estimated_remaining_ml` | `input_number.awms_200xl_ato_remaining_ml` | High | Done | Core AWMS inventory |
| `input_number.ato_last_volume_ml` | `input_number.awms_rodi_last_fill_volume_ml` | High | Done | Used by reservoir gauge |
| `input_number.awc_fill_last_volume_ml` | `input_number.awms_salt_last_fill_volume_ml` | High | Done | Used by reservoir gauge |
| `input_number.awms_200xl_last_volume_ml` | `input_number.awms_200xl_ato_last_fill_volume_ml` | High | Done | Last manual 200XL ATO fill amount |
| `input_number.awms_amount` | `input_number.awms_adjustment_amount` | High | Done | AWMS control helper |
| `input_select.awms_amount_unit` | `input_select.awms_adjustment_unit` | High | Done | AWMS control helper |
| `input_select.awms_action` | `input_select.awms_adjustment_action` | High | Done | AWMS control helper |
| `input_select.awms_reservoir` | `input_select.awms_source_reservoir` | High | Done | AWMS control helper |
| `input_select.awms_used_for` | `input_select.awms_workflow` | High | Done | AWMS control helper |
| `sensor.ato_remaining_percent` | `sensor.awms_rodi_remaining_percent` | High | Done | Template sensor likely depends on RODI helper |
| `sensor.fill_remaining_percent` | `sensor.awms_salt_remaining_percent` | High | Done | Template sensor likely depends on Salt helper |
| `sensor.awms_200xl_rodi_percent` | `sensor.awms_200xl_ato_remaining_percent` | High | Done | Template sensor uses the 94635 mL normal operating target |
| `sensor.ato_remaining_liters` | `sensor.awms_rodi_remaining_liters` | Medium | Done | Legacy template sensor |
| `sensor.fill_remaining_liters` | `sensor.awms_salt_remaining_liters` | Medium | Not found | Safe to skip if no dashboard/template references remain |
| `sensor.awms_rodi_remaining_gallons` | keep | Low | Already good | Already named clearly |
| `sensor.awms_salt_remaining_gallons` | keep | Low | Already good | Already named clearly |
| `sensor.awms_200xl_rodi_remaining_gallons` | `sensor.awms_200xl_ato_remaining_gallons` | Medium | Done | Rename from RODI to ATO because this is the 200XL ATO reservoir |
| `sensor.awms_rodi_percent` | `sensor.awms_rodi_remaining_percent` | Medium | Already done | More explicit |
| `sensor.awms_salt_percent` | `sensor.awms_salt_remaining_percent` | Medium | Already done | More explicit |
| `sensor.awms_preview_amount_ml` | `sensor.awms_adjustment_amount_ml` | Medium | Done | If kept, it should honor `input_select.awms_adjustment_unit` |
| `sensor.awms_preview_remaining` | `sensor.awms_adjustment_preview_remaining_gal` | Medium | Done | If kept, clarify displayed unit |
| `sensor.awms_validation` | `sensor.awms_adjustment_validation` | Medium | Done | AWMS workflow validation |

## 200XL ATO Runtime Automations

The 200XL ATO start automation stores the time when the Hydros ATO binary sensor turns on. This is runtime tracking, not a manual reservoir-fill event.

Current automation:

```yaml
alias: 200xl - ATO Start
triggers:
  - trigger: state
    entity_id:
      - binary_sensor.bedroom_aquarium_ato
    from:
      - "off"
    to:
      - "on"
actions:
  - action: input_datetime.set_datetime
    target:
      entity_id:
        - input_datetime.200xl_ato_start_time
    data:
      datetime: "{{ now().strftime('%Y-%m-%d %H:%M:%S') }}"
mode: single
```

Suggested helper names:

| Current entity | Suggested entity | Notes |
| --- | --- | --- |
| `input_datetime.200xl_ato_start_time` | `input_datetime.200xl_ato_started_at` | Use for Hydros ATO runtime start | DONE
| `binary_sensor.bedroom_aquarium_ato` | keep | Hydros-provided entity; rename only if you want a Home Assistant entity customization layer | DID NOT RENAME

Keep these concepts separate:

- `input_datetime.200xl_ato_started_at` for ATO runtime tracking.
- `input_datetime.awms_200xl_ato_last_filled_at` only if you want a separate helper for manual reservoir fill events.
- `input_number.awms_200xl_ato_last_fill_volume_ml` for manual reservoir fill volume.

The 200XL ATO stop automation calculates runtime, converts runtime to mL using the pump rate, stores the last ATO usage volume, and deducts that amount from the 200XL ATO reservoir estimate.

Current automation:

```yaml
alias: 200xl - ATO Stop
triggers:
  - trigger: state
    entity_id:
      - binary_sensor.bedroom_aquarium_ato
    from:
      - "on"
    to:
      - "off"
actions:
  - variables:
      pump_rate: 4.67
      runtime: |
        {{ as_timestamp(now()) -
           as_timestamp(states('input_datetime.200xl.ato_start_time')) }}
      volume: |
        {{ (runtime * pump_rate) | round(0) }}
      current_remaining: >
        {{ states('input_number.bedroom_ato_estimated_remaining_ml') |
        float(132490) }}
      remaining: |
        {{ [current_remaining - volume, 0] | max }}
  - action: input_number.set_value
    target:
      entity_id:
        - input_number.awms_200xl_last_volume_ml
    data:
      value: "{{ volume }}"
  - action: input_number.set_value
    target:
      entity_id:
        - input_number.bedroom_ato_estimated_remaining_ml
    data:
      value: "{{ remaining }}"
mode: single
```

Issues to fix during cleanup:

- `states('input_datetime.200xl.ato_start_time')` should be `states('input_datetime.200xl_ato_start_time')` in the current naming scheme.
- After renaming, use `states('input_datetime.200xl_ato_started_at')`.
- `pump_rate: 4.67` is mL/second, which matches the documented 280 mL/minute pump rate.
- The fallback value `float(132490)` should be updated to the AWMS 200XL ATO normal operating target of `94635`.

Suggested cleaned version after helper renames:

```yaml
alias: 200xl - ATO Stop
triggers:
  - trigger: state
    entity_id:
      - binary_sensor.bedroom_aquarium_ato
    from:
      - "on"
    to:
      - "off"
actions:
  - variables:
      pump_rate_ml_per_second: 4.67
      runtime_seconds: >
        {{ as_timestamp(now()) -
           as_timestamp(states('input_datetime.200xl_ato_started_at')) }}
      volume_ml: >
        {{ (runtime_seconds * pump_rate_ml_per_second) | round(0) }}
      current_remaining_ml: >
        {{ states('input_number.awms_200xl_ato_remaining_ml') | float(94635) }}
      remaining_ml: >
        {{ [current_remaining_ml - volume_ml, 0] | max }}
  - action: input_number.set_value
    target:
      entity_id:
        - input_number.awms_200xl_ato_last_fill_volume_ml
    data:
      value: "{{ volume_ml }}"
  - action: input_number.set_value
    target:
      entity_id:
        - input_number.awms_200xl_ato_remaining_ml
    data:
      value: "{{ remaining_ml }}"
mode: single
```

Naming note: `input_number.awms_200xl_ato_last_fill_volume_ml` is clear for manual reservoir fill volume, but this stop automation records ATO usage volume. If both values matter, create a separate helper:

```text
input_number.200xl_ato_last_usage_ml
```

## Known References To Update

Counts below are based on the current repo snippets plus the pasted dashboard YAML.

| Entity | Approx. references found |
| --- | ---: |
| `input_select.awms_amount_unit` | 89 |
| `input_select.awms_used_for` | 45 |
| `input_number.awms_amount` | 34 |
| `input_number.ato_estimated_remaining_ml` | 26 |
| `input_number.fill_estimated_remaining_ml` | 26 |
| `input_select.awms_reservoir` | 23 |
| `input_number.bedroom_ato_estimated_remaining_ml` | 17 |
| `input_select.awms_action` | 10 |
| Manual test helpers | 3-4 each |
| Maintenance date helpers | 1-2 each |

## Files/Areas To Update In This Repo

| Area | Files |
| --- | --- |
| Public documentation | `README.md`, `AGENTS.md`, `docs/index.md` |
| AWMS apply logic | `snippets/awms-apply-adjustment-full-script.yaml` |
| AWMS dashboard | `snippets/awms-water-management-expander.yaml` |
| AWMS workflow defaults | `snippets/awms-sync-workflow-defaults-automation.yaml` |
| Activity log card | `snippets/awms-recent-activity-card.yaml` |
| Full dashboard export | Aquarium dashboard YAML exported from Home Assistant |
| Template sensors | `template.yaml` |

## Template Sensors To Update

The current template snippet includes both older generic sensors and newer AWMS sensors.

Recommended cleanup:

| Current template sensor | Suggested action |
| --- | --- |
| `sensor.ato_remaining_liters` | Rename to `sensor.awms_rodi_remaining_liters`, or remove if gallons are now preferred everywhere |
| `sensor.fill_remaining_liters` | Rename to `sensor.awms_salt_remaining_liters`, or remove if gallons are now preferred everywhere |
| `sensor.ato_remaining_percent` | Replace with `sensor.awms_rodi_remaining_percent` |
| `sensor.fill_remaining_percent` | Replace with `sensor.awms_salt_remaining_percent` |
| `sensor.awms_rodi_remaining_gallons` | Keep |
| `sensor.awms_salt_remaining_gallons` | Keep |
| `sensor.awms_200xl_rodi_remaining_gallons` | Rename to `sensor.awms_200xl_ato_remaining_gallons` |
| `sensor.awms_rodi_percent` | Rename to `sensor.awms_rodi_remaining_percent` |
| `sensor.awms_salt_percent` | Rename to `sensor.awms_salt_remaining_percent` |
| `sensor.awms_200xl_rodi_percent` | Rename to `sensor.awms_200xl_ato_remaining_percent` |
| `sensor.awms_preview_amount_ml` | Update or remove. Current version assumes gallons and does not honor the AWMS unit selector. |
| `sensor.awms_preview_remaining` | Update or remove. The dashboard now calculates richer preview text inline. |
| `sensor.awms_validation` | Update or remove depending on whether validation is still shown in the dashboard. |

Capacity constants to standardize:

| Reservoir | Capacity |
| --- | ---: |
| Peninsula RODI | `111700` mL |
| Peninsula Salt | `111700` mL |
| 200XL ATO | `94635` mL |

The old 200XL percent template used `132490`; update that to `94635` so the dashboard shows percent of the normal 25 gallon operating fill.

If `sensor.awms_preview_amount_ml` is retained, use the selected unit:

```yaml
- name: "AWMS Adjustment Amount ML"
  unique_id: awms_adjustment_amount_ml
  unit_of_measurement: "mL"
  state: >
    {% set amount = states('input_number.awms_adjustment_amount') | float(0) %}
    {% if is_state('input_select.awms_adjustment_unit', 'mL') %}
      {{ amount | round(0) }}
    {% elif is_state('input_select.awms_adjustment_unit', 'L') %}
      {{ (amount * 1000) | round(0) }}
    {% elif is_state('input_select.awms_adjustment_unit', 'oz') %}
      {{ (amount * 29.5735) | round(0) }}
    {% elif is_state('input_select.awms_adjustment_unit', 'gal') %}
      {{ (amount * 3785.41) | round(0) }}
    {% else %}
      0
    {% endif %}
```

However, the current AWMS script intentionally converts units inline in service templates for Home Assistant 2026.7.1 compatibility. Keep that pattern in the script unless testing proves the template sensor is safe to use there.

## Home Assistant Areas To Search

In Home Assistant, search all YAML/config UI exports for each old entity ID.

Check:

- Dashboards / Lovelace raw configuration
- Script UI: `script.awms_apply_adjustment`
- Automation UI: AWMS workflow defaults
- Template sensors in `template.yaml`
- Any package files
- Any helper-backed gauge cards
- Logbook cards
- Statistics/history/utility meter cards if added later

## Suggested Migration Order

1. Export or back up the current Home Assistant configuration.
2. Create the new helpers in Home Assistant with the new names.
3. Copy current values from old helpers to new helpers.
4. Update template sensors to point at the new helpers.
5. Update AWMS scripts.
6. Update AWMS automations.
7. Update dashboard YAML.
8. Restart Home Assistant or reload helpers/templates/scripts/automations as appropriate.
9. Test preview-only dashboard states first.
10. Test one small AWMS adjustment.
11. Keep old helpers for a few days as rollback references.
12. Delete old helpers only after all references are gone.

## Validation Checklist

After renaming, confirm:

- RODI gauge shows the correct gallons and percent.
- Salt gauge shows the correct gallons and percent.
- 200XL ATO gauge uses `94635` mL as its normal operating max.
- Make New Salt preview still calculates the Salt fill amount correctly.
- 200XL ATO workflow deducts RODI and increases 200XL ATO.
- Plants workflow deducts RODI only.
- 200XL Water Change workflow deducts Salt only.
- Activity log card shows the new helper entities.
- No dashboard card displays `unknown` or `unavailable`.
- Home Assistant logs show no template errors for old entity IDs.

## Practical Publishing Option

For GitHub, you do not have to rename the live Home Assistant instance immediately.

A lower-risk option is:

1. Keep the current local entity names.
2. Publish a clean public example using the standardized names.
3. Include an `ENTITY_MAP.md` showing how your local names map to the public names.

That gives readers a clean example while avoiding a high-risk live migration until you are ready.
