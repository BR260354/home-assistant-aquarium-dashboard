---
title: Aquarium Mobile Dashboard
description: Portrait-oriented Home Assistant monitoring and maintenance for the Aquarium Automation Suite.
---

# Aquarium Mobile dashboard

**Aquarium Mobile** is the everyday phone interface for AAS. It uses native Home Assistant views for **Aquarium** and **Maintenance**, keeping navigation available in the Companion app without nesting the whole dashboard inside a custom tab card.

The interface is monitoring-first, with Home Assistant controls for selected HYDROS outputs alongside status, alerts, trends, guarded maintenance actions, and the Aquarium Water Management Suite (AWMS) workflow. ReefBeat and HYDROS remain useful for device setup and advanced configuration.

## Aquarium

The single-tank Aquarium view replaces Overview and Peninsula. Current Alerts
and Active Devices lead the page. Active Devices shows HYDROS Mode and every
water/feeding HYDROS output that is running or manually On. AI Blades and WEv2
are excluded from Active Devices.

Temperature, pH, ORP, Salinity, Alkalinity, Nitrate, Phosphates, and Calcium are
visible together. Each parameter has its own graph color. ReefControl level
entities drive the four live probe indicators; manual chemistry graphs retain
their 30-day windows. Salt Mix temperature and Magnesium remain in Additional
Sensors.

Water Movement combines the HYDROS and ReefRun dials in one blue-backed panel.
Names use (H) for HYDROS and (RS) for Red Sea.

Equipment & Outputs combines the former Hydros Outputs, Systems, and Other
Devices groups in one collapsed expander directly below Dosing. Lighting &
WaveEngine, Water &
Feeding, Systems, and Accessories keep related controls together. All three AI
Blades, WEv2, and the original eight HYDROS outputs have confirmed Auto/On/Off
choices. Wattage appears where a power sensor is available. Red Sea equipment
with an HA safety or schedule automation uses (RS · HA); accessories managed
through Home Assistant use (HA). CO₂ Scrubber Bypass remains status-only.

Dosing and Filtration remain separate expanders. HYDROS Mode controls are now
in Maintenance Status, while its current value stays visible in Active Devices.

The screenshots below the Maintenance section predate the November 1 layout.
For the verified current configuration and recovery notes, see
[Live dashboard snapshots](../dashboards/README.md).

## Maintenance

Maintenance combines a guarded **Dose Maintenance** quick action, consumable and calibration status, AWMS water adjustments, reservoir estimates, and recent inventory activity. Advanced ReefBeat configuration and testing logs remain in ReefBeat.

<img src="assets/screenshots/mobile/mobile-maintenance-status.jpeg" alt="Mobile maintenance quick actions and status" width="360">
<img src="assets/screenshots/mobile/mobile-maintenance-water-management.jpeg" alt="Mobile AWMS water-management workflow" width="360">
<img src="assets/screenshots/mobile/mobile-maintenance-activity.jpeg" alt="Mobile reservoir status and recent AWMS activity" width="360">

## Design choices

- Native Home Assistant views provide reliable mobile navigation.
- Two-column cards balance information density with portrait readability.
- Expanders are used selectively for long detail sections, not for primary health and flow information.
- Alerts show exceptions; normal sensor states do not consume space.
- ReefBeat and HYDROS remain the control surfaces for device setup and advanced configuration; selected HYDROS output modes are available directly in Home Assistant.
- The same entities, templates, and alert rules feed both Mobile and Tablet.

[Return to the project overview](index.html) · [View the Tablet dashboard](tablet-dashboard.html)
