# Aquarium Automation Suite

Aquarium Automation Suite (AAS) is my Home Assistant dashboard and automation project for two Red Sea aquariums and their shared water-mixing station.

I have used Home Assistant since 2019. Hydros and Red Sea each do a good job of managing their own equipment, but I wanted one place where I could see both aquariums, understand the state of the supporting equipment, and manage the water shared between them. AAS is that common layer.

The dashboard is the product: helpers, templates, scripts, and automations exist to turn a large collection of devices and entities into a clear view of the aquarium system.

> [!NOTE]
> This repository documents a personal system and is shared as a reference for other aquarium and Home Assistant hobbyists. It is not a ready-to-install package, and local entity names and hardware will differ.

## The aquarium system

### Red Sea Peninsula 500 G2

The Peninsula is a mixed reef and the primary display aquarium in the main living area of our townhouse.

| Platform | Equipment |
| --- | --- |
| Hydros | Kraken, X4, Launch, Minnow, WaveEngine v2, X2, and XS |
| Red Sea | ReefRun return pump, RSK-300 skimmer, ReefMat 500, and ReefDose 4 |
| Other | UV sterilizer, Avast Marine feeder, and ozone |

The Peninsula also uses automatic top off (ATO) and automatic water changes (AWC). Its Hydros collective includes the controllers at the mixing station.

### Red Sea 200XL G2

The 200XL is a separate soft coral and anemone aquarium with a couple of clownfish and chromis.

| Platform | Equipment |
| --- | --- |
| Hydros | Launch, WaveEngine LE, WiFi Quad, and WiFi Feeder |
| Red Sea | ReefDose 4 |

It has an independent ATO reservoir and uses manual water changes. The ATO reservoir is manually filled from the Peninsula RODI reservoir.

### Mixing station

The mixing station is built around two nominal 30-gallon Brute reservoirs: one for RODI water and one for saltwater. Hydros X2, XS, and XD controllers manage the station. The XD drives three Kamoer dosing pumps used for ATO and AWC.

The mixing-station controllers belong to the same Hydros collective as the Peninsula. I also use the station for non-aquarium jobs, including watering plants, so controller runtime alone does not tell the whole inventory story. This is why water tracking became a major part of AAS.

Two TP-Link smart power strips provide additional switched outlets across the system.

## What Home Assistant adds

AAS brings the independent systems together in one interface:

- A single dashboard for both aquariums
- Water parameters and equipment state at a glance
- RODI, saltwater, and 200XL ATO reservoir estimates
- Guided water-removal and transfer workflows
- Aquarium maintenance controls organized around real tasks
- A foundation for activity history, usage statistics, and forecasting

The first major subsystem is the **Aquarium Water Management Suite (AWMS)**. It tracks water even when it leaves the mixing station for something other than an aquarium.

## Water inventory

Inventory is always stored internally in milliliters and converted to gallons for display.

```text
1 US gallon = 3,785.41 mL
```

| Reservoir | Working capacity | How it is used |
| --- | ---: | --- |
| Peninsula RODI | 111,700 mL / 29.5 gal | ATO, salt mixing, 200XL ATO fills, plants, and other uses |
| Peninsula saltwater | 111,700 mL / 29.5 gal | Automatic and manual water changes |
| 200XL ATO | 75,708 mL / about 20 gal | Independent top-off supply for the 200XL |

The 200XL ATO container can physically hold about 113,562 mL / 30 gallons, but AWMS uses the normal operating maximum of about 20 gallons. Filling it through AWMS deducts water from Peninsula RODI and adds the same amount to the 200XL ATO estimate. Future Hydros runtime tracking will deduct top-off usage automatically.

### Current workflows

| Workflow | Source | Inventory result |
| --- | --- | --- |
| Plants | Peninsula RODI | Decrease RODI |
| Make New Salt | Peninsula RODI | Decrease RODI and fill saltwater to capacity |
| 200XL ATO | Peninsula RODI | Decrease RODI and increase 200XL ATO |
| 200XL Water Change | Peninsula saltwater | Decrease saltwater |
| Other | RODI or saltwater | Decrease the selected reservoir |

## Dashboard preview

The interface is designed primarily for a mobile portrait layout.

| Peninsula | 200XL |
| --- | --- |
| <img src="docs/assets/screenshots/dashboard-01.png" alt="Peninsula water parameters and status" width="360"> | <img src="docs/assets/screenshots/dashboard-02.png" alt="200XL water parameters and status" width="360"> |

### Water management

<img src="docs/assets/screenshots/dashboard-03.png" alt="Reservoir gauges and AWMS quick actions" width="360"> <img src="docs/assets/screenshots/dashboard-04.png" alt="Make New Salt preview and apply controls" width="360">

### Equipment views

<img src="docs/assets/screenshots/dashboard-05.png" alt="Water flow equipment" width="280"> <img src="docs/assets/screenshots/dashboard-06.png" alt="Dosing equipment" width="280"> <img src="docs/assets/screenshots/dashboard-07.png" alt="Filtration and general equipment" width="280">

## Repository guide

This repository contains reusable portions of the working configuration rather than a complete Home Assistant backup.

| File | Purpose |
| --- | --- |
| [`docs/index.md`](docs/index.md) | Public GitHub Pages project overview |
| [`snippets/awms-water-management-stack.yaml`](snippets/awms-water-management-stack.yaml) | Main Water Management dashboard panel |
| [`snippets/awms-water-management-expander.yaml`](snippets/awms-water-management-expander.yaml) | Collapsible Water Management panel |
| [`snippets/awms-reservoir-gauge-cards.yaml`](snippets/awms-reservoir-gauge-cards.yaml) | Reservoir status gauges |
| [`snippets/awms-template-sensors.yaml`](snippets/awms-template-sensors.yaml) | AWMS display and calculation sensors |
| [`snippets/awms-apply-adjustment-full-script.yaml`](snippets/awms-apply-adjustment-full-script.yaml) | Script UI logic for inventory changes |
| [`snippets/awms-sync-workflow-defaults-automation.yaml`](snippets/awms-sync-workflow-defaults-automation.yaml) | Workflow-aware helper defaults |
| [`snippets/awms-recent-activity-card.yaml`](snippets/awms-recent-activity-card.yaml) | Optional recent activity card |
| [`docs/helper-rename-plan.md`](docs/helper-rename-plan.md) | Helper and entity migration notes |

Home Assistant helpers and the AWMS apply script are created through the Home Assistant UI. Templates remain in `template.yaml`, while automations remain in `automations.yaml`. The snippets are intended to be copied into the corresponding UI editor or configuration file and adapted to local entities.

## Dashboard components

The visual system uses [Mushroom](https://github.com/piitaya/lovelace-mushroom), [card-mod](https://github.com/thomasloven/lovelace-card-mod), [Expander Card](https://github.com/MelleD/lovelace-expander-card), [Gauge Card Pro](https://github.com/benjamin-dcs/gauge-card-pro), [Bubble Card](https://github.com/Clooos/Bubble-Card), [Mini Graph Card](https://github.com/kalkih/mini-graph-card), [Simple Tabs](https://github.com/agoberg85/home-assistant-simple-tabs), and [Vertical Stack In Card](https://github.com/ofekashery/vertical-stack-in-card). Community frontend components are installed through [HACS](https://www.hacs.xyz/).

Existing Gauge Card Pro reservoir cards are treated as production components and enhanced rather than replaced. The dashboard uses blue for RODI, purple for saltwater, and green for 200XL ATO so that water sources remain visually consistent throughout the interface.

## Integrations and acknowledgements

This project would not be possible without the community integrations that bring aquarium equipment into Home Assistant:

- **Red Sea ReefBeat:** [Elwinmage/ha-reefbeat-component](https://github.com/Elwinmage/ha-reefbeat-component), created and maintained by **Elwinmage**, provides local Home Assistant support for Red Sea equipment including ReefRun, ReefMat, and ReefDose.
- **HYDROS:** [Bitf1ip/ha-hydros](https://github.com/Bitf1ip/ha-hydros), created and maintained by **Bitf1ip**, provides Home Assistant entities for CoralVue HYDROS controllers.

Thank you to both authors for making it possible to bring these otherwise separate aquarium ecosystems into one dashboard. AAS also builds on [Home Assistant](https://www.home-assistant.io/), [HACS](https://www.hacs.xyz/), and the frontend projects linked above.

This project has been designed collaboratively with ChatGPT over multiple design and implementation sessions.

## Project direction

- **Version 1:** dashboard, reservoir inventory, manual adjustments, and transfers
- **Version 1.1:** activity log, statistics, and automatic Hydros refill handling
- **Version 1.2:** 200XL ATO runtime tracking and usage analytics
- **Version 2:** forecasting, maintenance planning, consumables, and predictive depletion

Reliability, readability, maintainability, and a consistent interface take priority over adding features quickly.

## Disclaimer

This is an independent personal project. It is not affiliated with or endorsed by Red Sea, CoralVue, TP-Link, the integration authors, or Home Assistant. Aquarium automation can move water and control life-support equipment; review entity names, limits, fail-safes, and physical safeguards before adapting any example to your system.
