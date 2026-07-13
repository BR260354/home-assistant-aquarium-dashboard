---
title: Aquarium Automation Suite
description: One Home Assistant dashboard for two Red Sea aquariums, a Hydros-controlled mixing station, and shared water inventory.
---

# Aquarium Automation Suite

Aquarium Automation Suite (AAS) is my Home Assistant dashboard and automation project for two Red Sea aquariums and their shared water-mixing station.

I have run Home Assistant since 2019. Hydros and Red Sea each provide their own view of the equipment they manage, but I wanted one place where I could see both aquariums and the systems that support them. AAS brings those separate ecosystems together and presents them around the jobs I actually perform.

The dashboard is the product. The automation underneath exists to make the system easier to understand and operate.

## My aquariums

### Red Sea Peninsula 500 G2

The Peninsula is a mixed reef and our primary display aquarium, located in the main living area of our townhouse.

| Platform | Equipment |
| --- | --- |
| Hydros | Kraken, X4, Launch, Minnow, WaveEngine v2, X2, and XS |
| Red Sea | ReefRun return pump, RSK-300 skimmer, ReefMat 500, and ReefDose 4 |
| Other | UV sterilizer, Avast Marine feeder, and ozone |

The aquarium uses automatic top off and automatic water changes. The Hydros equipment at the nearby mixing station is part of the same collective.

### Red Sea 200XL G2

The 200XL is a soft coral and anemone aquarium with a couple of clownfish and chromis.

| Platform | Equipment |
| --- | --- |
| Hydros | Launch, WaveEngine LE, WiFi Quad, and WiFi Feeder |
| Red Sea | ReefDose 4 |

The 200XL is an independent aquarium with its own ATO reservoir and manual water changes. Its ATO reservoir is filled manually from the Peninsula RODI supply.

### Mixing station

The mixing station uses two nominal 30-gallon Brute reservoirs for RODI and saltwater. Hydros X2, XS, and XD controllers manage the station, and the XD drives three Kamoer dosing pumps used for ATO and AWC. These controllers share the Peninsula Hydros collective.

The station is useful beyond the aquariums—RODI water also goes to plants and other household jobs. That makes a separate inventory model valuable: the controller knows when a pump ran, but Home Assistant can describe where the water went and estimate what remains.

Two TP-Link smart power strips add more controllable outlets to the overall system.

## One dashboard

The Home Assistant interface provides:

- A combined view of both aquariums
- Water parameters and equipment status
- RODI, saltwater, and 200XL ATO reservoir estimates
- Guided water-removal and transfer workflows
- Maintenance controls organized around real tasks
- A path toward activity history, usage statistics, and forecasting

![Peninsula water parameters and status](assets/screenshots/dashboard-01.png)

![200XL water parameters and status](assets/screenshots/dashboard-02.png)

## Aquarium Water Management Suite

The first major AAS subsystem is the **Aquarium Water Management Suite (AWMS)**. It tracks the water shared by the aquariums, mixing station, and other uses.

All inventory is stored internally in milliliters. Gallons are a display unit only.

```text
1 US gallon = 3,785.41 mL
```

| Reservoir | Working capacity | Notes |
| --- | ---: | --- |
| Peninsula RODI | 111,700 mL / 29.5 gal | Automatically filled under Hydros control |
| Peninsula saltwater | 111,700 mL / 29.5 gal | Mixed from Peninsula RODI |
| 200XL ATO | 75,708 mL / about 20 gal | Filled from Peninsula RODI; physical capacity is about 30 gal |

When the 200XL ATO reservoir is filled through AWMS, the same volume is deducted from Peninsula RODI and added to the 200XL ATO estimate. The normal AWMS maximum is about 20 gallons even though the container can physically hold about 30 gallons.

### Supported workflows

| Workflow | Source | Result |
| --- | --- | --- |
| Plants | RODI | Deducts Peninsula RODI |
| Make New Salt | RODI to saltwater | Deducts RODI and fills saltwater to capacity |
| 200XL ATO | RODI to 200XL ATO | Deducts RODI and increases the 200XL ATO estimate |
| 200XL Water Change | Saltwater | Deducts Peninsula saltwater |
| Other | User-selected RODI or saltwater | Deducts the selected reservoir |

![Reservoir gauges and AWMS quick actions](assets/screenshots/dashboard-03.png)

![Make New Salt preview and apply controls](assets/screenshots/dashboard-04.png)

## Equipment views

The same visual language is used for flow, dosing, filtration, and general equipment so that a large system remains easy to scan on a phone.

![Water flow equipment](assets/screenshots/dashboard-05.png)

![Dosing equipment](assets/screenshots/dashboard-06.png)

![Filtration and general equipment](assets/screenshots/dashboard-07.png)

## How it is built

AAS uses Home Assistant helpers, templates, automations, Script UI scripts, and a Lovelace dashboard. Helpers and the main AWMS script are intentionally created through the Home Assistant UI rather than declared as repository YAML.

Important inventory entities:

| Purpose | Entity |
| --- | --- |
| Peninsula RODI inventory | `input_number.awms_rodi_remaining_ml` |
| Peninsula saltwater inventory | `input_number.awms_salt_remaining_ml` |
| 200XL ATO inventory | `input_number.awms_200xl_ato_remaining_ml` |
| Adjustment amount | `input_number.awms_adjustment_amount` |
| Selected unit | `input_select.awms_adjustment_unit` |
| Selected action | `input_select.awms_adjustment_action` |
| Source reservoir | `input_select.awms_source_reservoir` |
| Workflow | `input_select.awms_workflow` |
| Apply adjustment | `script.awms_apply_adjustment` |

Repository examples:

| File | Purpose |
| --- | --- |
| [`awms-water-management-stack.yaml`]({{ site.github.repository_url }}/blob/main/snippets/awms-water-management-stack.yaml) | Main Water Management panel |
| [`awms-water-management-expander.yaml`]({{ site.github.repository_url }}/blob/main/snippets/awms-water-management-expander.yaml) | Collapsible Water Management panel |
| [`awms-reservoir-gauge-cards.yaml`]({{ site.github.repository_url }}/blob/main/snippets/awms-reservoir-gauge-cards.yaml) | Reservoir status gauges |
| [`awms-template-sensors.yaml`]({{ site.github.repository_url }}/blob/main/snippets/awms-template-sensors.yaml) | AWMS display and calculation sensors |
| [`awms-apply-adjustment-full-script.yaml`]({{ site.github.repository_url }}/blob/main/snippets/awms-apply-adjustment-full-script.yaml) | Script UI inventory logic |
| [`awms-sync-workflow-defaults-automation.yaml`]({{ site.github.repository_url }}/blob/main/snippets/awms-sync-workflow-defaults-automation.yaml) | Workflow-aware defaults |
| [`awms-recent-activity-card.yaml`]({{ site.github.repository_url }}/blob/main/snippets/awms-recent-activity-card.yaml) | Optional activity history |

These are excerpts from a personal configuration, not a turnkey installation. Anyone adapting them should review entity names, capacities, limits, and safeguards for their own hardware.

## Interface components

The dashboard uses community frontend projects installed with [HACS](https://www.hacs.xyz/).

| Project | Role in AAS |
| --- | --- |
| [Mushroom](https://github.com/piitaya/lovelace-mushroom) | Cards, chips, and primary interaction patterns |
| [card-mod](https://github.com/thomasloven/lovelace-card-mod) | Styling and visual polish |
| [Expander Card](https://github.com/MelleD/lovelace-expander-card) | Collapsible maintenance sections |
| [Gauge Card Pro](https://github.com/benjamin-dcs/gauge-card-pro) | Reservoir gauges |
| [Bubble Card](https://github.com/Clooos/Bubble-Card) | Section structure |
| [Mini Graph Card](https://github.com/kalkih/mini-graph-card) | Usage and history graphs |
| [Simple Tabs](https://github.com/agoberg85/home-assistant-simple-tabs) | Aquarium dashboard tabs |
| [Vertical Stack In Card](https://github.com/ofekashery/vertical-stack-in-card) | Grouped layouts |

RODI is consistently blue, saltwater is purple, and 200XL ATO is green. Existing Gauge Card Pro reservoir cards are treated as production components and enhanced rather than redesigned.

## Integration credits

Special thanks to the developers whose community integrations make the unified dashboard possible:

- **Elwinmage** created and maintains the [Red Sea ReefBeat integration](https://github.com/Elwinmage/ha-reefbeat-component), which brings supported Red Sea equipment into Home Assistant.
- **Bitf1ip** created and maintains the [HYDROS integration](https://github.com/Bitf1ip/ha-hydros), which brings CoralVue HYDROS controller data into Home Assistant.

Their work supplies the aquarium entities on which AAS depends. Thank you also to the teams and contributors behind [Home Assistant](https://www.home-assistant.io/), [HACS](https://www.hacs.xyz/), and the dashboard projects listed above.

This project has been designed collaboratively with ChatGPT over multiple design and implementation sessions.

## Roadmap

- **Version 1:** inventory, dashboard, manual adjustments, and transfers
- **Version 1.1:** activity log, statistics, and automatic Hydros refill handling
- **Version 1.2:** 200XL ATO runtime tracking and usage analytics
- **Version 2:** forecasting, maintenance planning, consumables, and predictive reservoir depletion

Reliability, readability, maintainability, and a consistent interface take priority over adding features quickly.

## Disclaimer

AAS is an independent personal project and is not affiliated with or endorsed by Red Sea, CoralVue, TP-Link, the integration authors, or Home Assistant. Aquarium automation can move water and control life-support equipment. Validate every entity, limit, fail-safe, and physical safeguard before adapting an example to another system.

[View the repository]({{ site.github.repository_url }})
