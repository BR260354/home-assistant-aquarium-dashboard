# Water management

## Authority and units

HYDROS owns the Peninsula ATO/AWC process and runtime signals. ReefBeat does
not control ATO/AWC. AWMS stores estimated inventory in Home Assistant.

Always store and calculate volume in milliliters. Convert only for display:

`1 US gallon = 3785.41 mL`

## Live inventory and configuration

| Purpose | Entity |
| --- | --- |
| RODI estimate | `input_number.awms_rodi_remaining_ml` |
| Saltwater estimate | `input_number.awms_salt_remaining_ml` |
| ATO pump rate | `input_number.peninsula_ato_pump_rate_ml_min` |
| AWC fill pump rate | `input_number.peninsula_awc_fill_pump_rate_ml_min` |
| RODI usable capacity | `input_number.peninsula_rodi_capacity_ml` |
| Saltwater usable capacity | `input_number.peninsula_salt_capacity_ml` |

Initial configuration was 400.2 mL/min for both pump rates and 111700 mL for
both capacities. Those are setup context only: query the helpers for the live
values before calculating anything.

## Diagnostic rules

- A reservoir helper is an estimate, not a direct level measurement.
- Use HYDROS optical/level states to qualify inventory estimates.
- When HYDROS water production completes at the high sensor, the deterministic
  automation resets RODI inventory to the current capacity helper.
- ATO and AWC volume calculations must use the current pump-rate helpers, not
  hard-coded rates.
- Sump ATO Dry is expected during active AWC and should not be presented as a
  true alert in that condition. Overflow remains an alert.
- Distinguish AWC Drain, Fill, and ATO phases when their live states are
  available.
- Report calculations with the source entities and mark them as estimates.

## AWMS workflow

AWMS supports guided Remove and Transfer operations through Home Assistant UI
helpers and `script.awms_apply_adjustment`. It also records recent activity.
Hermes v1 may explain the preview, inventory impact, or history but must not
run the script or alter workflow helpers.

The decommissioned 200XL reservoir and workflows are not part of live AAS
status. Ignore them unless the user explicitly asks about handoff material.
