# AAS architecture

AAS treats Home Assistant as the live, configurable source of truth. Hermes
may interpret and explain the system, but it does not own current device state,
calibration values, or deterministic safety behavior.

## Four layers

1. **Device integrations** — HYDROS, ReefBeat, TP-Link, and other equipment
   integrations publish current device state and configuration.
2. **Aquarium configuration and state** — Home Assistant helpers store values
   the integrations do not know, such as calibrated pump rates, usable
   reservoir capacities, and reservoir estimates.
3. **Deterministic automation** — Home Assistant performs water accounting,
   equipment schedules, alerting, and safety interlocks.
4. **Interpretation** — Hermes can read the first three layers to explain
   conditions and support troubleshooting, but does not replace them. The v1
   aquarium-management skill is deliberately read-only.

## Ownership boundaries

| Information | Authority |
| --- | --- |
| HYDROS ATO/AWC state and runtime signals | HYDROS integration |
| Red Sea device state and configuration | ReefBeat integration |
| Red Sea maintenance intervals and completion dates | ReefBeat integration |
| RODI and saltwater estimates | AWMS helpers |
| ATO/AWC calibrated pump rates | AAS configuration helpers |
| Reservoir usable capacities | AAS configuration helpers |
| Water accounting and equipment safety | Home Assistant automations |
| Diagnosis and natural-language explanation | Interpretation layer |

ReefBeat is not involved in Peninsula ATO/AWC control or water accounting.
Those workflows remain HYDROS-driven.

HYDROS control is intentionally outside the Home Assistant and Hermes scope.
Advanced HYDROS configuration remains in the HYDROS app.

## Authoritative AAS configuration helpers

Inventory and capacity are stored in milliliters. Pump calibration is stored
in milliliters per minute and converted to milliliters per second only inside
the volume calculation.

| Helper | Initial value | Purpose |
| --- | ---: | --- |
| `input_number.peninsula_ato_pump_rate_ml_min` | 400.2 mL/min | HYDROS ATO/AWC drain volume calculation |
| `input_number.peninsula_awc_fill_pump_rate_ml_min` | 400.2 mL/min | HYDROS AWC fill volume calculation |
| `input_number.peninsula_rodi_capacity_ml` | 111,700 mL | Usable RODI capacity and full reset value |
| `input_number.peninsula_salt_capacity_ml` | 111,700 mL | Usable saltwater capacity |

The initial pump-rate values preserve the prior production value of
6.67 mL/second. Future calibration changes should be made in these helpers,
not in automation code.

## ReefBeat maintenance and alerts

Device-specific Red Sea maintenance remains integration-owned. AAS does not
duplicate ReefRun, ReefDose, ReefMat, or ReefPower maintenance dates and
intervals with parallel helpers.

The installed `ReefBeat watch (redsea)` blueprint supplies deterministic
alerts for device messages, abnormal mode, unavailable devices, overdue
maintenance/calibration, and supported battery conditions. The AAS automation
is `automation.aquarium_reefbeat_watch` and initially notifies Richard's iPhone.

ReefBeat Cloud also exposes the configured Peninsula quick actions as Home
Assistant switches. ReefBeat owns each action's device membership and return
delays; AAS displays the switches without recreating their sequencing:

- `switch.aquarium_peninsula_maintenance` — Return Maintenance
- `switch.aquarium_peninsula_skimmer_maintenance` — Skimmer Maintenance
- `switch.aquarium_peninsula_doser_maintenance_2` — Doser Maintenance
- `switch.aquarium_peninsula_emergency` — ReefBeat-integrated emergency shutdown

Hermes v1 may inspect and explain these states but must not activate or cancel
them.

## Decommissioned 200XL material

The live Mobile and Tablet dashboards no longer include the 200XL. Its handoff
files remain under `handoff/` so the receiving Home Assistant installation can
use them. Historical screenshots and legacy snippets may still describe the
old two-aquarium system; they are not live AAS configuration.

RSMAT-250 has been removed from the current ReefBeat integration. Any remaining
RSMAT-250 reference is historical or part of the 200XL handoff, not a live
Peninsula dependency.
