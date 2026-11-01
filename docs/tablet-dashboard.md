---
title: Aquarium Tablet Dashboard
description: Full-width iPad monitoring and HYDROS output controls for the Aquarium Automation Suite.
---

# Aquarium Tablet dashboard

**Aquarium Tablet** is the landscape, full-width AAS interface intended for the iPad Pro beside the Peninsula. It shares the live Peninsula entities, alerts, and maintenance workflows with Aquarium Mobile, and uses the available width to keep related equipment visible together.

## Aquarium

Aquarium and Maintenance are the two views. The former Overview was removed.
Current Alerts and Active Devices lead the Aquarium view, followed by eight
water-quality graphs: Temperature, pH, ORP, Salinity, Alkalinity, Nitrate,
Phosphates, and Calcium. The four manual chemistry graphs keep their 30-day
history windows.

One blue-backed Water Movement panel combines the four HYDROS circulation
pumps and the two ReefRun pumps, marked (H) and (RS). Its six columns reflow
for narrower displays.

Equipment & Outputs sits directly below Dosing, is collapsed by default, and
spans the full width. It
combines lighting/WaveEngine, water/feeding outputs, systems, and accessories.
The three AI Blades and WEv2 join the original eight HYDROS outputs with
confirmed Auto/On/Off choices and available power readings. AWC Drain and Fill
use their existing running helpers without inventing separate power readings.
Active Devices shows running or manually On water/feeding HYDROS outputs and the current
HYDROS Mode, excluding AI Blades and WEv2. Mode controls now sit in Maintenance Status.

H means HYDROS, RS means Red Sea, and HA means Home Assistant. RS · HA marks
Red Sea equipment governed by HA safety/schedule automation. CO₂ Scrubber
Bypass remains status-only. Salt Mix and Magnesium stay under Additional
Sensors; dosing and ReefMat information remain available below.

For the verified current configuration and recovery notes, see
[Live dashboard snapshots](../dashboards/README.md). Existing screenshots are
historical and predate this November 1 update.

## Maintenance

The landscape maintenance view puts quick actions and maintenance status above a two-column working area. AWMS actions remain on the left while reservoir status and recent activity remain visible on the right, making the preview and inventory effect easy to compare before Apply is pressed.

![Tablet maintenance overview](assets/screenshots/tablet/tablet-maintenance-overview.png)

![Tablet AWMS workflow and recent activity](assets/screenshots/tablet/tablet-maintenance-water.png)

## Design choices

- Full-width layout is optimized for landscape iPad use.
- Related systems share rows instead of becoming one long vertical stack.
- Equipment & Outputs stays collapsed until needed, then opens across the full width.
- Cyan output icons, tile borders, and active radio choices match the full-width group border.
- Wider cards preserve complete labels, graphs, schedule names, and equipment state.
- The visual hierarchy and color rules match Aquarium Mobile.

[Return to the project overview](index.html) · [View the Mobile dashboard](mobile-dashboard.html)
