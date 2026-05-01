# Session: Structured Business Hours (Schedule Feature)
Date: 2026-04-30
Platform: WordPress

## What Was Done

Implemented the structured hours feature on the WordPress platform — the first Tier 1 roadmap item from `FEATURES.md` / `STORE_LOCATOR_NEXT_FEATURES_CLAUDE.md`.

Replaced the free-text `hours` field with a structured `schedule` JSON blob stored in a new DB column. This unlocks open-now logic, badge display, and filtering in the widget without breaking any existing locations that only have the legacy `hours` text.

**DB migration (v1.1):**
- Added `schedule TEXT` column to `wp_mpp_store_locations` via `dbDelta` in `class-db.php`
- Existing rows default to NULL / empty — widget falls back to text `hours` if `schedule` is absent

**Admin UI changes (`admin-ui/src/LocationModal.jsx`):**
- Added a weekly hours editor — one row per day (Mon–Sun), each with open/closed toggle and start/end time inputs
- Schedule saved as JSON matching the canonical schema (see below)
- Existing `hours` text field preserved on the form for backward compatibility

**Widget changes (`widget/src/store-locator.jsx`):**
- Widget parses `schedule` JSON from the API response
- Derives per-location: `is_open_now`, `next_open_time`, `status_label`
- Displays an open/closed badge on location cards when schedule is present
- Added "Open Now" filter chip to the sidebar — filters list to locations currently open
- Falls back to rendering plain `hours` text when no structured schedule exists

**API:**
- `schedule` field included in public `GET /locations` response
- `schedule` accepted on `POST /admin/locations` and `PUT /admin/locations/{id}`
- No breaking changes to existing response shape

## Key Decisions

**Keep `hours` text field.** Existing locations only have free-text hours. Rather than forcing a migration or breaking the display, `schedule` is additive — the widget shows the badge and filter only when structured data is present, otherwise renders the legacy text.

**Store schedule as a JSON TEXT column.** Avoids a complex relational schema for a field that varies per location. The canonical shape is:
```json
{
  "timezone": "America/Toronto",
  "weekly": {
    "mon": [{ "start": "09:00", "end": "17:00" }],
    "tue": [{ "start": "09:00", "end": "17:00" }]
  },
  "exceptions": [
    { "date": "2026-12-25", "closed": true }
  ]
}
```

**WordPress only for now.** Shopify and Webflow are `📋 Planned` — see `FEATURES.md`. When porting, the schema and widget logic should be identical; only the storage layer differs.

## Files Changed

| File | Change |
|---|---|
| `includes/class-db.php` | Added `schedule TEXT` column, bumped DB version to 1.1 |
| `includes/class-locations.php` | `schedule` included in insert/update/cast logic |
| `includes/class-rest-api.php` | `schedule` in public and admin response/accept |
| `admin-ui/src/LocationModal.jsx` | Weekly hours editor UI |
| `widget/src/store-locator.jsx` | Schedule parsing, open-now logic, badge, filter chip |

## Gotchas / Fixes

**Timezone handling.** Open-now calculation must use the location's own timezone (stored in `schedule.timezone`), not the server or browser timezone. The widget runs client-side so `Intl.DateTimeFormat` with the stored IANA timezone string is the correct approach.

**`schedule` NULL vs empty object.** `dbDelta` defaults new column to NULL. Widget and admin must handle both NULL (no schedule set) and `{}` (schedule cleared) as equivalent — treat both as "fall back to hours text".

**`dbDelta` and column additions.** Adding a column to an existing table via `dbDelta` requires the full `CREATE TABLE` statement to be re-run with the new column present — `dbDelta` diffs against the live schema and applies only the delta. Running it again is idempotent.

## Next Steps

- Port structured hours to **Shopify** — update `models/settings.js` defaults, location save route, admin LocationModal, widget
- Port to **Webflow** — same pattern
- Add `exceptions` (holiday hours) editor in admin UI — currently only weekly recurring hours are editable; exception dates are stored but not yet exposed in the form
- Consider exposing an "Open Today" filter alongside "Open Now"
- Update `FEATURES.md` when Shopify and Webflow ports ship
