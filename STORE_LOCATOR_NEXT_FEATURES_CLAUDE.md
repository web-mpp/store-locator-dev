# Store Locator Next Features Guide for Claude Code

## Purpose

This document is a practical implementation roadmap for the MPP Store Locator product family.

The goal is to keep the app a **general location discovery platform** that can serve:

- Store locator
- Event locator
- Where to buy
- Dealer locator
- Service center locator
- Showroom finder
- Pickup / drop-off locator

Do not optimize future work around one narrow client type. Favor **generic building blocks** that can be configured for many use cases.

---

## Current Baseline

The current apps already support:

- Location CRUD
- CSV import/export
- Auto-geocoding
- Map + list display
- Search
- City / province filtering
- Near-me geolocation
- Distance sorting when user location is known
- Directions links
- Up to 3 CTAs per location
- Deep appearance customization
- Multi-platform delivery across Shopify, Webflow, and WordPress

The next wave of work should focus on making the app:

- More flexible as a data model
- Better for discovery and filtering
- More useful for merchants with many locations
- More measurable
- Still easy to configure

---

## Product Principles

When implementing new features, follow these rules:

1. Prefer generic field names over vertical-specific names.
2. Prefer optional, composable features over hardcoded business logic.
3. Keep the public widget simple, but make the admin more powerful.
4. Avoid features that only make sense for one industry unless they are implemented as configurable metadata.
5. Reuse the same schema and interaction patterns across Shopify, Webflow, and WordPress where possible.
6. Treat "event locator", "where to buy", and "dealer locator" as presets of the same core product.

---

## Recommended Next Features

## Tier 1: Highest ROI

These are the best next features because they expand the product meaningfully without forcing a rewrite.

### 1. Structured Hours + Open Now

#### Why

`hours` currently behaves like display text. Converting this into structured data unlocks:

- Open now filtering
- Closed / opening soon badges
- Day-by-day display
- Holiday hours
- Event date/time support

This helps stores, events, service centers, and showrooms.

#### Recommendation

- Keep the existing `hours` text field for backward compatibility.
- Add a new structured field such as `schedule`.
- Use a generic schema that supports both recurring hours and one-off date-based entries.

Example shape:

```json
{
  "schedule": {
    "timezone": "America/Toronto",
    "weekly": {
      "mon": [{ "start": "09:00", "end": "17:00" }],
      "tue": [{ "start": "09:00", "end": "17:00" }]
    },
    "exceptions": [
      { "date": "2026-12-25", "closed": true },
      { "date": "2026-07-01", "slots": [{ "start": "10:00", "end": "14:00" }] }
    ]
  }
}
```

#### Implementation Notes

- Add parsing and validation in server routes before save.
- Add admin UI with a simple weekly editor first.
- Preserve text-based `hours` rendering if `schedule` is absent.
- In the widget, derive:
  - `is_open_now`
  - `next_open_time`
  - `status_label`
- Add optional filters:
  - `Open now`
  - `Open today`

#### Priority

Very high.

---

### 2. Generic Tags / Categories / Attributes

#### Why

This is the single most important change for making the app work across multiple use cases.

Examples:

- Store locator: `pickup`, `delivery`, `wheelchair-accessible`
- Event locator: `family-friendly`, `free`, `ticketed`
- Where to buy: `retailer`, `distributor`, `online-only`
- Dealer locator: `service`, `parts`, `showroom`

#### Recommendation

Add generic metadata fields rather than custom fields for each vertical.

Suggested additions:

```json
{
  "categories": ["retailer", "service-center"],
  "tags": ["pickup", "wheelchair-accessible", "featured"],
  "attributes": {
    "brand": "Brand A",
    "appointment_required": true,
    "inventory_program": "premium"
  }
}
```

#### Implementation Notes

- Start with `categories` and `tags`.
- Add `attributes` only if needed after that.
- In admin:
  - support manual editing
  - support CSV import mapping
  - support filter chips
- In widget:
  - support category filter
  - optionally show badges on cards
- In appearance settings:
  - make badges optional and styleable later

#### Priority

Very high.

---

### 3. Radius Filter for Near-Me Results

#### Why

Distance sorting already exists. Radius filtering is the natural next step and is useful in every locator variant.

#### Recommendation

Add optional radius filtering after geolocation or postal-code search.

Suggested options:

- 5 km
- 10 km
- 25 km
- 50 km
- 100 km

#### Implementation Notes

- Add optional `radius_km` query param to public and admin location endpoints.
- Apply radius filter only when `lat/lng` is present.
- Keep sorting by distance after filtering.
- In widget UI:
  - show radius select only after a user location is known
  - preserve existing "Near me" button
- Later, allow mi/km display based on locale.

#### Priority

Very high.

---

### 4. Postal / ZIP Search With Geocoded Lookup

#### Why

Many users expect to enter a postal code, not click "Near me".

This is especially important for:

- Where to buy
- Dealer locator
- Service area search

#### Recommendation

Add a dedicated location search input that geocodes user-entered place text or postal code into coordinates.

#### Implementation Notes

- Add server endpoint like `/api/geocode-search?q=...`
- Use Mapbox geocoding for user search terms
- Return normalized result:

```json
{
  "query": "M5V 2T6",
  "latitude": 43.642,
  "longitude": -79.387,
  "label": "Toronto, Ontario, Canada"
}
```

- Feed result into existing distance-based location fetch
- Keep text search for locations separate from location-origin search
- In UI, do not overload one input with both behaviors unless UX remains clear

#### Priority

High.

---

### 5. Analytics and Event Tracking

#### Why

Once the widget is operationally strong, the next big value layer is measurement.

Merchants will want to know:

- Which locations get the most views
- Which searches are performed
- How often users click directions
- Which CTAs perform best
- Whether near-me is actually used

#### Recommendation

Track interactions as generic events.

Suggested events:

- `locator_loaded`
- `search_used`
- `near_me_used`
- `radius_changed`
- `filter_applied`
- `location_selected`
- `directions_clicked`
- `cta_clicked`

#### Implementation Notes

- Create a server-side event ingestion endpoint
- Store events in a simple append-only table or JSON log layer depending on platform maturity
- Use a consistent schema:

```json
{
  "event": "cta_clicked",
  "location_id": "abc123",
  "site_id": "xyz",
  "context": {
    "cta_slot": "cta2",
    "query": "Toronto",
    "radius_km": 25
  },
  "timestamp": "2026-04-30T12:00:00Z"
}
```

- Keep analytics internal first
- Add GA4 / GTM forwarding later as optional integration

#### Priority

High.

---

## Tier 2: Strong Product Upgrades

### 6. Marker Clustering

#### Why

This becomes important once clients have many locations in dense areas.

#### Recommendation

- Add optional clustering for map views
- Keep it configurable per widget instance or global setting

#### Implementation Notes

- Use native Mapbox source/layer clustering rather than many independent marker instances when clustering is enabled
- Preserve current custom marker mode for non-clustered use
- Start with simple count bubbles
- Clicking a cluster should zoom in

#### Priority

High for larger datasets.

---

### 7. Featured Locations and Sort Rules

#### Why

Sometimes "nearest" should not be the only ranking logic.

#### Recommendation

Add generic fields:

- `featured`
- `sort_weight`

#### Implementation Notes

- Default sort behavior:
  - if no user location: featured first, then sort weight, then alphabetic
  - if user location exists: optionally nearest first, with featured acting as a tie-breaker or override
- Make sort mode configurable in settings
- Avoid hardcoding industry-specific ranking rules

#### Priority

High.

---

### 8. Richer Card Content With Optional Badges and Media

#### Why

Many use cases need more context than name and address.

#### Recommendation

Add optional fields:

- `image_url`
- `badge_text`
- `short_label`
- `secondary_text`

Use generic names so they work for stores, events, and dealers.

#### Implementation Notes

- Keep card layout backward-compatible
- Add appearance toggles for:
  - show image
  - show badge
  - show description
  - show schedule status
- For events, `secondary_text` can hold date or venue info
- For stores, it can hold service notes

#### Priority

Medium-high.

---

### 9. Duplicate Detection and Data Quality Tools

#### Why

As imports grow, merchant data quality becomes a major support burden.

#### Recommendation

Add admin-side utilities for:

- duplicate detection
- missing coordinate detection
- missing CTA detection
- invalid URL detection
- empty required field detection

#### Implementation Notes

- Add a lightweight "Data Health" panel
- Start with deterministic checks, not AI or fuzzy heavy logic
- Surface actionable counts:
  - missing coordinates
  - duplicate address candidates
  - broken CTA URLs

#### Priority

Medium-high.

---

### 10. Preset Configurations

#### Why

The product should stay generic, but onboarding can still feel vertical-aware.

#### Recommendation

Add starter presets, not separate products.

Suggested presets:

- `Store Locator`
- `Event Locator`
- `Where to Buy`
- `Dealer Locator`
- `Service Center`

Each preset should only prefill:

- labels
- filter defaults
- CTA labels
- badge visibility
- field recommendations

#### Implementation Notes

- Do not fork the schema by preset
- Presets should write normal settings values only
- Allow users to switch presets without losing data

#### Priority

Medium-high.

---

## Tier 3: Later, But Valuable

### 11. Saved Search State and Shareable URLs

#### Why

This improves usability and makes filtered results linkable.

#### Recommendation

- Sync search and filters to URL params
- Restore widget state on load

#### Implementation Notes

- Persist:
  - text query
  - category
  - city / province
  - radius
  - selected location
- Keep this optional for embeds where URL mutation is undesirable

---

### 12. Localization and Unit Preferences

#### Recommendation

- Add support for:
  - custom labels
  - multi-language UI strings
  - km/mi display
  - locale-aware address formatting

#### Priority

Medium.

---

### 13. Integrations

#### Recommendation

Prioritize simple integrations that reinforce the locator's role as a configurable data-driven widget.

Good candidates:

- Google Sheets sync
- Airtable sync
- webhook push / pull
- public API tokens for external management

#### Priority

Medium.

---

## Data Model Direction

To keep the app generic, evolve the location schema toward this shape:

```json
{
  "id": "loc_123",
  "name": "Downtown Location",
  "address": "123 Main St",
  "city": "Toronto",
  "province": "ON",
  "postal_code": "M5V 2T6",
  "country": "Canada",
  "latitude": 43.64,
  "longitude": -79.38,
  "phone": "",
  "email": "",
  "description": "",
  "hours": "",
  "schedule": {},
  "categories": [],
  "tags": [],
  "attributes": {},
  "image_url": "",
  "badge_text": "",
  "secondary_text": "",
  "featured": false,
  "sort_weight": 0,
  "cta_label": "",
  "cta_url": "",
  "cta2_label": "",
  "cta2_url": "",
  "cta3_label": "",
  "cta3_url": "",
  "directions_url": "",
  "directions_show": true
}
```

This keeps the current structure intact while adding extensibility.

---

## Recommended Implementation Order

Implement in this order unless there is a client-specific reason not to:

1. Structured hours + open now
2. Categories + tags
3. Radius filter
4. Postal / ZIP origin search
5. Analytics events
6. Featured locations + sort rules
7. Marker clustering
8. Rich card content
9. Data health tools
10. Presets

This order gives the best combination of product value, reuse, and low migration risk.

---

## Cross-Platform Build Strategy

For every new feature, follow this sequence:

1. Extend the shared data model first.
2. Update import/export support second.
3. Update admin forms third.
4. Update public API fourth.
5. Update widget UI last.

Use the Shopify, Webflow, and WordPress versions as parallel implementations of the same product, not three different products.

Where possible:

- keep response shapes aligned
- keep field names identical
- keep settings names identical
- keep widget behavior equivalent

Platform-specific auth or hosting differences are fine. Product behavior should still match.

---

## Claude Code Instructions

When Claude Code works from this file:

1. Do not introduce vertical-specific schema unless there is no generic alternative.
2. Default to backward-compatible additions.
3. Preserve current CSV workflows.
4. Keep the widget usable with incomplete data.
5. Prefer shipping small generic primitives over large custom one-off features.
6. If a feature could apply to stores, events, dealers, and showrooms, it is likely a good fit.
7. If a feature only applies to one narrow workflow, consider implementing it as `tags`, `attributes`, presets, or labels instead.

---

## Summary

The app should evolve from a "store locator" into a **configurable location discovery widget system**.

The next best features are the ones that add:

- structured time logic
- flexible metadata
- stronger proximity search
- better ranking
- richer analytics
- better data quality

That path keeps the product broad enough to support store locator, event locator, and where-to-buy use cases without splitting into separate codebases or overly specialized features.
