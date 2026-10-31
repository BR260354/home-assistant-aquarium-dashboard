# ReefBeat integration inventory

Inventory refreshed: 2026-08-17

Installed components:

- `Elwinmage/ha-reefbeat-component` v2.4.0
- `Elwinmage/ha-reef-card` v0.3.1

Both were current when this inventory was recorded.

## Loaded Red Sea controllers

- ReefDose4 - 4-Part
- ReefDose4-Extra
- ReefMat 500
- ReefRun / RSK-300
- ReefControl Pro
- ReefPower 8

RSMAT-250 is no longer loaded in the integration.

## ReefBeat Cloud quick actions

The ReefBeat Cloud connection exposes the configured Peninsula quick actions
as Home Assistant switches:

| Dashboard label | Entity |
| --- | --- |
| Return Maintenance | `switch.aquarium_peninsula_maintenance` |
| Skimmer Maintenance | `switch.aquarium_peninsula_skimmer_maintenance` |
| Doser Maintenance | `switch.aquarium_peninsula_doser_maintenance_2` |
| Emergency | `switch.aquarium_peninsula_emergency` |

The action membership and delayed device returns are configured in ReefBeat.
AAS must not duplicate those delays in parallel automations. Emergency affects
only equipment integrated with ReefBeat; HYDROS-owned ATO/AWC equipment is not
part of that action.

## Integration-owned maintenance

The current entities expose maintenance configuration and completion actions
for the equipment that supports them. Examples include:

- ReefRun intake-strainer, motor/rotor, skimmer sensor, and venturi work
- ReefMat carbon replacement and roll state
- ReefDose head calibration and tube replacement
- Controller maintenance-mode switches

AAS should display or aggregate these entities rather than create duplicate
maintenance dates and intervals.

## ReefControl Pro

The Peninsula dashboard uses the ReefControl Pro salinity-probe temperature,
pH, ORP, and salinity entities plus their integration-provided level entities.
The binary leak detector is treated as healthy when it is `on`; the separate
leak value and level entities remain hidden while they report unusable states.

## ReefBeat Watch

The official integration blueprint `Elwinmage/redsea_alerts.en.yaml` is
installed. The active automation `automation.aquarium_reefbeat_watch` monitors
the Red Sea integration and sends alerts to Richard's iPhone. It does not
control or monitor the HYDROS-owned ATO/AWC process.

## Interpretation boundary

Live ReefBeat entities are authoritative for current state, schedules,
configuration, container levels, calibration, and maintenance. The Hermes v1
skill may correlate and explain those values, but it does not store a parallel
copy or issue device-control commands.
