# Live aquarium dashboards

These JSON snapshots were read back from Home Assistant after the November 1,
2026 single-tank layout update:

- `dashboard-aquarium.json`: Aquarium Mobile
- `aquarium-tablet.json`: Aquarium Tablet

Both dashboards now have Aquarium and Maintenance views. The Aquarium view
retains its `peninsula` URL path so existing direct links continue working.
Overview was removed after its water-quality color treatment was carried into
the main view. Eight parameters are visible, with the four manual chemistry
graphs retaining their 30-day history windows.

Water Movement combines four HYDROS pumps and two ReefRun pumps. Equipment &
Outputs sits directly below Dosing, is collapsed by default on both dashboards,
and spans the full tablet width when opened. It groups lighting/WaveEngine, water/feeding, systems, and
accessories. H means HYDROS, RS means Red Sea, and HA means Home Assistant.
RS · HA identifies Red Sea equipment with HA safety/schedule automation.

All twelve HYDROS output selects have confirmed Auto/On/Off actions. Power is
shown for the ten outputs with discovered power sensors; AWC Drain and Fill
have running helpers but no separate discovered power sensors. Active Devices
includes water/feeding outputs that are running or manually On, plus the existing
intermittent-device statuses. AI Blades and WEv2 are excluded from Active
Devices. HYDROS Mode is shown there and controlled in Maintenance Status. CO₂ Scrubber Bypass remains status-only.

The live ReefControl references currently use `_2` IDs, and the ORP probe is
`orp_285`. They were verified against Home Assistant before deployment. The
older IDs documented elsewhere in the repository were not substituted into
these working snapshots. Salinity is displayed in PSU.

Validation: output status templates rendered in Home Assistant; both saved
configurations were read back; Mobile, Tablet, Maintenance, and the expanded
Tablet controls were visually inspected. No equipment services were invoked.

## Recovery

The complete pre-change dashboard API responses, including their original
configuration hashes, are in
`../backups/dashboards/2026-11-01-before-single-tank/`.
To restore one dashboard, read its latest live config/hash, then write the
backup's `config` object to the matching dashboard URL through the dashboard
API using that latest hash. Do not use the backup's old hash, edit `.storage`,
or restore the entire Home Assistant instance for a dashboard-only rollback.
