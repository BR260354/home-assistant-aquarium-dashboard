---
name: aquarium-management
description: Diagnose and explain the live Red Sea Peninsula aquarium through Home Assistant. Use for aquarium status briefs, active alerts, water quality, HYDROS ATO/AWC and circulation, ReefBeat/ReefControl/ReefRun/ReefMat/ReefDose/ReefPower state, maintenance, dosing discrepancies, reservoir inventory, equipment safety, or questions about why aquarium equipment is on, off, abnormal, or unavailable.
---

# Aquarium Management

Interpret the Aquarium Automation Suite (AAS) as an operating manual layered on
live Home Assistant state. Never treat values recorded in this skill as current
measurements or configuration.

## Work read-only

This v1 skill is diagnostic and explanatory only.

- Query Home Assistant, but do not call services, press buttons, toggle
  switches, change helpers, acknowledge maintenance, or edit configuration.
- Never dose, prime, calibrate, start maintenance, cancel maintenance, trigger
  Emergency, or change an equipment schedule.
- Never attempt HYDROS control. Direct advanced HYDROS work to the HYDROS app.
- If the user asks for an action, explain the relevant control and current
  safety context, then state that v1 did not execute it.

## Follow the diagnostic workflow

1. Classify the request: status, alert, water quality, equipment, dosing,
   maintenance, or water inventory.
2. Read only the reference file needed for that class.
3. Query the smallest useful set of Home Assistant entities. Prefer exact
   entity IDs; search the entity registry by device or friendly name when an ID
   is missing or renamed.
4. Treat `unknown` and `unavailable` as data-quality conditions, not equipment
   failures, until corroborated by another entity.
5. Correlate state across systems before drawing a conclusion. For example,
   explain a stopped Sterilizer using both its ReefPower state and ReefRun
   return safety inputs.
6. Report the condition, supporting evidence, likely interpretation, and the
   safest next check. Distinguish facts from inferences.

Do not quote an entity's old state from a reference file. If Home Assistant
cannot be queried, say that live verification is unavailable and provide only
the relevant operating rule.

## Apply source-of-truth precedence

Use this order when information overlaps:

1. Live device integration state for device measurements and configuration.
2. Home Assistant helpers for AAS-only calibration, capacities, and estimates.
3. Home Assistant automations for deterministic schedules and interlocks.
4. This skill for relationships, interpretation, and diagnostic procedure.

ReefBeat owns Red Sea state, configuration, maintenance, calibration, and
supported schedules. HYDROS owns ATO/AWC signals and HYDROS equipment state.
AWMS helpers own reservoir estimates. Do not recreate these facts in prose.

## Route reference loading

- Read [references/system-model.md](references/system-model.md) for scope,
  ownership, entity groups, and equipment relationships.
- Read [references/diagnostic-playbooks.md](references/diagnostic-playbooks.md)
  for status briefs, alerts, water-quality problems, equipment failures, and
  dosing discrepancies.
- Read [references/maintenance-and-safety.md](references/maintenance-and-safety.md)
  for quick actions, ReefBeat maintenance, Sterilizer/Ozone/CO2 rules, and
  control boundaries.
- Read [references/water-management.md](references/water-management.md) for
  RODI, saltwater, ATO/AWC accounting, capacities, pump rates, and AWMS.

## Keep responses operational

Lead with the answer. For a normal status request, use this compact order:

1. Overall state: normal, attention, or data incomplete.
2. Active issues and their evidence.
3. Water quality and life-support summary.
4. Maintenance or inventory items that require attention.

Omit normal details that do not help answer the question. Include entity IDs
only when they help troubleshoot or verify a mapping.

## Exclude retired scope

Do not include the decommissioned 200XL or RSMAT-250 in live status,
diagnostics, or recommendations. Files under `handoff/` and legacy screenshots
are historical material for another Home Assistant installation.
