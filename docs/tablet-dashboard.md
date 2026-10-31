---
title: Aquarium Tablet Dashboard
description: Full-width iPad monitoring and HYDROS output controls for the Aquarium Automation Suite.
---

# Aquarium Tablet dashboard

**Aquarium Tablet** is the landscape, full-width AAS interface intended for the iPad Pro beside the Peninsula. It shares the live Peninsula entities, alerts, and maintenance workflows with Aquarium Mobile, and uses the available width to keep related equipment visible together.

## Overview

The Overview view summarizes Peninsula health, alerts, active devices, water quality, and circulation. The detailed Peninsula view carries the equipment controls and monitoring cards; Maintenance contains AWMS and service actions.

[View the current full-page Aquarium Tablet capture](assets/screenshots/tablet/tablet-peninsula-current.png)

## Peninsula

Alerts and Other Active Devices lead the Peninsula view, followed by the four water-quality graphs and the HYDROS WaveEngine and ReefRun groups. The HYDROS circulation dials show the current pump speed together with the active schedule and direction. Testing uses a wide multi-card row, and dosing and filtration equipment remain grouped in their own sections.

The **Other Devices** group under Filtration & Systems includes the first
HYDROS TDS reading labeled **TDS - Post DI**. Alerts show it when it rises above
zero. The second **Pre DI** reading is not currently exposed as a separate
Home Assistant entity.

### HYDROS Outputs

The HYDROS Outputs panel is a full-width card rather than an expander. Its cyan outline matches the surrounding equipment panels, and each output is grouped with its own **Auto**, **On**, and **Off** radio-style controls. The layout uses four columns on wide screens, two on tablet widths, and one on narrow screens.

The controls cover **Water Feed**, **Fresh Stir**, **Feed Stir**, **AWC ATO**, **AWC Drain**, **AWC Fill**, **Avast Feed**, and **Display Fans**. Each output shows **Running** when its running indicator is active, the selected **On** or **Off** mode when manually set, or **Auto · Idle** while waiting for its schedule or condition. Drain and Fill status uses the AWC start/stop helpers because the integration does not expose separate running sensors for those phases. A full-width **HYDROS Mode** row adds radio choices for EcoFlow, Feeding, Low Power, Normal, and Water Change.

## Maintenance

The landscape maintenance view puts quick actions and maintenance status above a two-column working area. AWMS actions remain on the left while reservoir status and recent activity remain visible on the right, making the preview and inventory effect easy to compare before Apply is pressed.

![Tablet maintenance overview](assets/screenshots/tablet/tablet-maintenance-overview.png)

![Tablet AWMS workflow and recent activity](assets/screenshots/tablet/tablet-maintenance-water.png)

## Design choices

- Full-width layout is optimized for landscape iPad use.
- Related systems share rows instead of becoming one long vertical stack.
- The Hydros Outputs controls stay visible without an expander and resize for tablet and phone widths.
- Cyan output icons, tile borders, and active radio choices match the full-width group border.
- Wider cards preserve complete labels, graphs, schedule names, and equipment state.
- The visual hierarchy and color rules match Aquarium Mobile.

[Return to the project overview](index.html) · [View the Mobile dashboard](mobile-dashboard.html)
