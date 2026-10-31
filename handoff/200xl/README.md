# 200XL Home Assistant Handoff

This folder is a self-contained handoff for the Red Sea 200XL after it moves to
a different Home Assistant installation. It does **not** change or remove
anything from the original aquarium system.

## Included files

- `dashboard-200xl.yaml` — complete one-view Lovelace dashboard based on the
  current Aquarium Mobile 200XL view, plus an 18-liter ATO reservoir section.
- `automations-200xl-ato.yaml` — ATO runtime inventory tracking and the guarded
  “Mark Reservoir Full” action.

## Design assumptions

- ATO reservoir operating capacity: **18,000 mL / 18 L / approximately 4.75 gal**.
- Inventory is stored in milliliters. Liters, gallons, and percent are display
  conversions only.
- Estimated ATO pump rate: **280 mL/minute** or **4.6667 mL/second**.
- The Hydros integration provides an entity that is `on` only while the ATO
  output is actively pumping.
- The ReefBeat integration provides the ReefMat 250 entities.
- The dashboard monitors Hydros and ReefBeat; detailed configuration remains in
  their respective apps.

## 1. Install integrations and dashboard cards

Install and configure these integrations before importing the dashboard:

1. Hydros: `Bitf1ip/ha-hydros`
2. ReefBeat: `Elwinmage/ha-reefbeat-component`

Install these frontend cards through HACS:

1. Mushroom
2. Button Card
3. Mini Graph Card
4. card-mod
5. layout-card
6. Expander Card

Restart Home Assistant after installing frontend resources if HACS requests it.
Confirm that normal Hydros and ReefBeat entity cards work before continuing.

## 2. Create the helpers in the UI

Create helpers from **Settings → Devices & services → Helpers → Create helper**.
After each helper is created, open its settings and set the entity ID exactly as
shown below. Do not define these helpers in YAML.

### ATO inventory helpers

| Helper type | Name | Required entity ID | Settings |
| --- | --- | --- | --- |
| Number | 200XL ATO Remaining mL | `input_number.200xl_ato_remaining_ml` | Min `0`, max `18000`, step `1`, unit `mL`, display mode `Input field` |
| Number | 200XL ATO Last Usage mL | `input_number.200xl_ato_last_usage_ml` | Min `0`, max `18000`, step `1`, unit `mL`, display mode `Input field` |
| Date and/or time | 200XL ATO Started At | `input_datetime.200xl_ato_started_at` | Enable both date and time |
| Button | 200XL ATO Mark Full | `input_button.200xl_ato_mark_full` | Icon `mdi:water-plus` |

After the automations are installed and the physical reservoir is full, press
**Mark Reservoir Full** once. That initializes the estimate to 18,000 mL.

### ATO percentage template helper

Create a **Template → Sensor** helper:

- Name: `200XL ATO Remaining Percent`
- Entity ID: `sensor.200xl_ato_remaining_percent`
- Unit: `%`
- State class: `Measurement`
- State template:

```jinja
{{ ((states('input_number.200xl_ato_remaining_ml') | float(0) / 18000) * 100) | round(0) }}
```

The dashboard turns the 200XL alert card red when this value is below 20%.

### Manual water-test helpers

These retain the chemistry cards from the original dashboard.

| Helper type | Name | Required entity ID | Suggested settings |
| --- | --- | --- | --- |
| Number | 200XL Alkalinity dKH | `input_number.200xl_alkalinity_dkh` | Min `0`, max `20`, step `0.01`, unit `dKH` |
| Number | 200XL Nitrate NO3 ppm | `input_number.200xl_nitrate_no3_ppm` | Min `0`, max `100`, step `0.1`, unit `ppm` |
| Number | 200XL Phosphate PO4 ppm | `input_number.200xl_phosphate_po4_ppm` | Min `0`, max `5`, step `0.001`, unit `ppm` |

Set these values whenever a manual test is recorded. Their graphs show roughly
five readings across 30 days, matching the original dashboard.

## 3. Map integration entities

The portable dashboard uses stable, purpose-based entity IDs. The entities
created on the recipient installation will probably have different prefixes.
For each Hydros or ReefBeat entity, find the matching function under
**Settings → Devices & services → Entities**, open its settings, and rename its
entity ID to the canonical value below.

### Hydros

| Function | Canonical entity ID used by the package |
| --- | --- |
| Alerts | `sensor.200xl_alerts` |
| MQTT health | `sensor.200xl_mqtt_health` |
| Display temperature | `sensor.200xl_temperature` |
| pH | `sensor.200xl_ph` |
| Sump level | `sensor.200xl_sump_level` |
| ATO running | `binary_sensor.200xl_ato` |
| OctoPulse 2 output | `sensor.200xl_octopulse_2` |
| Return output | `sensor.200xl_return` |
| Sterilizer | `sensor.200xl_sterilizer` |
| Display fan power | `sensor.200xl_fans_power` |
| Helios heater | `binary_sensor.200xl_helios_heater` |
| Cabinet lights | `binary_sensor.200xl_cabinet_light` |
| Minnow head 1 active | `binary_sensor.200xl_minnow_doser_1` |
| Minnow head 1 dosed today | `sensor.200xl_minnow_doser_1_dosed_today` |
| Minnow head 1 reservoir remaining | `sensor.200xl_minnow_doser_1_reservoir_remaining` |
| Minnow head 2 active | `binary_sensor.200xl_minnow_doser_2` |
| Minnow head 2 dosed today | `sensor.200xl_minnow_doser_2_dosed_today` |
| Minnow head 2 reservoir remaining | `sensor.200xl_minnow_doser_2_reservoir_remaining` |

If Hydros does not expose Minnow head 2, leave those three entities absent. The
unused head can remain as not configured or its dashboard card can be removed.

### ReefBeat / ReefMat 250

| Function | Canonical entity ID used by the package |
| --- | --- |
| Total roll usage | `sensor.200xl_reefmat_total_usage` |
| Remaining roll length | `sensor.200xl_reefmat_remaining_length` |
| Usage today | `sensor.200xl_reefmat_today_usage` |
| Daily average usage | `sensor.200xl_reefmat_daily_average_usage` |
| Estimated days remaining | `sensor.200xl_reefmat_days_till_end_of_roll` |

Renaming should be done before importing the dashboard and automations. If an
entity is optional and not installed, its card may show unavailable; remove that
card from the raw dashboard configuration if desired.

## 4. Import the automations

The automation file contains two automations.

### UI method

1. Go to **Settings → Automations & scenes → Automations**.
2. Create a new empty automation.
3. Open the three-dot menu and choose **Edit in YAML**.
4. Copy the first list item from `automations-200xl-ato.yaml`, remove its
   leading list dash, de-indent the remaining block by one level, and save it.
5. Repeat for the second list item.

### YAML-managed method

Append both list items to `automations.yaml`, validate the configuration, and
reload automations. Do not duplicate the top-level list dash if the target file
uses a different include structure.

Keep the automations disabled until `binary_sensor.200xl_ato` has been observed
changing from `off` to `on` while the top-off pump is physically running and
back to `off` when it stops.

## 5. Verify the pump estimate

The supplied automation uses the existing estimate of 280 mL/minute. Verify it
after moving the equipment:

1. Put the ATO outlet into a measuring container.
2. Run the pump for exactly 60 seconds using Hydros.
3. Measure the delivered volume.
4. Divide the measured mL by 60.
5. Replace `pump_rate_ml_per_second: 4.6667` in the runtime automation with the
   measured result.

The inventory is an estimate. Pump wear, head pressure, tubing length, and
calibration affect the real flow rate. Hydros remains responsible for safely
controlling the ATO; Home Assistant only observes runtime and estimates usage.

## 6. Import the dashboard

1. Go to **Settings → Dashboards → Add dashboard**.
2. Name it `200XL Aquarium` and use a URL such as `200xl-aquarium`.
3. Open the new dashboard and take control if Home Assistant created it using a
   strategy.
4. Enter dashboard edit mode, open the three-dot menu, and choose
   **Raw configuration editor**.
5. Replace the raw configuration with the contents of
   `dashboard-200xl.yaml` and save.
6. Refresh the browser or Companion app once so all custom cards load.

## 7. Acceptance checklist

- Temperature and pH show live values and 12-hour graphs.
- Hydros alerts are green when clear and red when an exception exists.
- OctoPulse and return dials show current output percentages.
- ReefMat usage and days remaining are populated.
- Minnow status reflects the heads actually exposed by Hydros.
- The ATO activity appears in Other Active Devices only while pumping.
- Pressing Mark Reservoir Full sets the estimate to 18,000 mL.
- A measured ATO run deducts a believable volume.
- The alert card reports ATO Reservoir Low below 20%.

## Transfer safety

Do not delete or rename the original installation's 200XL entities until the
recipient setup has passed this checklist. Exporting this package does not move
integration credentials, cloud accounts, schedules, calibration data, or
device ownership. Transfer those separately through Hydros and ReefBeat using
their supported account/device-transfer procedures.
