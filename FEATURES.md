# MPP Store Locator — Feature Source of Truth

This file is the canonical feature registry for all three MPP Store Locator platforms.

**Platforms:** Shopify · Webflow · WordPress  
**Last updated:** 2026-04-30

When a feature ships on a platform, update the status cell here. When a new feature is planned, add it to the roadmap table before implementing.

---

## How to Use This File

- Before implementing any feature, check this file to understand current state across all platforms.
- After shipping, update the status here.
- If a feature behaves differently on one platform, document the difference in the "Notes" column.
- Claude Code: load only the relevant platform's `CLAUDE.md` for implementation details. This file tells you *what* needs to change; the platform CLAUDE.md tells you *how*.

---

## Status Key

| Symbol | Meaning |
|---|---|
| ✅ | Live and working |
| 🚧 | In progress |
| 📋 | Planned, not started |
| ❌ | Not applicable to this platform |
| ⚠️ | Partial — see notes |

---

## Core Features (All Platforms)

These features are live on all three platforms and must remain in sync when modified.

| Feature | Shopify | Webflow | WordPress | Notes |
|---|---|---|---|---|
| Location CRUD (create, read, update, delete) | ✅ | ✅ | ✅ | |
| Bulk delete | ✅ | ✅ | ✅ | |
| Search locations (text) | ✅ | ✅ | ✅ | Admin and widget |
| Filter by city | ✅ | ✅ | ✅ | |
| Filter by province | ✅ | ✅ | ✅ | |
| CSV import with column mapping wizard | ✅ | ✅ | ✅ | |
| CSV export | ✅ | ✅ | ✅ | |
| Auto-geocoding on save/import | ✅ | ✅ | ✅ | Mapbox; WP uses PHP `wp_remote_get()` |
| Directions button (per location) | ✅ | ✅ | ✅ | Auto-generates Google Maps URL from address |
| `directions_show` toggle per location | ✅ | ✅ | ✅ | Hides button per location |
| Up to 3 CTA buttons per location | ✅ | ✅ | ✅ | Renders only when label + URL both set |
| Interactive Mapbox map | ✅ | ✅ | ✅ | Public `pk.*` token required |
| Location list panel | ✅ | ✅ | ✅ | |
| Near-me geolocation | ✅ | ✅ | ✅ | Browser Geolocation API |
| Distance sorting | ✅ | ✅ | ✅ | Activates when user location is known |
| Full appearance customization | ✅ | ✅ | ✅ | ~40 fields; see settings schema in `CLAUDE.md` |
| Live appearance preview in admin | ✅ | ✅ | ✅ | Sticky right panel |
| Unsaved-change prompts | ✅ | ✅ | ✅ | On tab switch and page unload |
| Layout modes (split, full-map, list-only) | ✅ | ✅ | ✅ | |
| Sidebar position (left/right) | ✅ | ✅ | ✅ | |
| Custom Google Fonts | ✅ | ✅ | ✅ | |
| Card styles (background, border, shadow) | ✅ | ✅ | ✅ | |
| Custom map marker color and style | ✅ | ✅ | ✅ | |
| Custom map style (Mapbox style URL) | ✅ | ✅ | ✅ | |
| Mobile layout options | ✅ | ✅ | ✅ | |
| CTA button styles (solid, outline, ghost) | ✅ | ✅ | ✅ | Configurable per CTA slot (1–3) + directions |
| Public API — locations endpoint | ✅ | ✅ | ✅ | Open CORS, no auth |
| Public API — settings endpoint | ✅ | ✅ | ✅ | Open CORS, no auth |
| Admin API — CRUD endpoints | ✅ | ✅ | ✅ | Platform-specific auth (see below) |

---

## Platform-Specific Features

These features exist on one platform only due to platform architecture requirements.

### Shopify Only

| Feature | Status | Notes |
|---|---|---|
| Shopify OAuth + embedded admin | ✅ | `@shopify/shopify-app-express` v7, App Bridge |
| Shopify theme editor block | ✅ | `extensions/store-locator/blocks/store-locator.liquid` |
| Global vs local settings (theme block overrides) | ✅ | `localSettings` merged over `globalAppearance` per block instance |
| App Proxy storefront URL | ✅ | `/apps/mpp-store-locator/api` — permanent URL stable across dev tunnels |
| Polaris admin UI | ✅ | Shopify design system; not present on Webflow or WP |
| File-based session storage | ✅ | `web/data/sessions.json` via `FileSessionStorage` |
| Per-shop JSON data storage | ✅ | `web/data/shops/{domain}/locations.json` |

### Webflow Only

| Feature | Status | Notes |
|---|---|---|
| Webflow OAuth auth | ✅ | Scopes: `sites:read custom_code:read custom_code:write` |
| Per-site HMAC admin tokens | ✅ | `HMAC-SHA256(ADMIN_API_KEY, siteId)` — issued at OAuth callback |
| Automatic widget injection | ✅ | Webflow Custom Code API — idempotent on re-install |
| Reconnect banner on revoked token | ✅ | Amber banner in admin if Webflow token is revoked |
| Designer Extension (sidebar panel) | ✅ | Built, `bundle.zip` ready for Webflow marketplace upload |
| Multi-tenant per-site JSON storage | ✅ | `server/data/sites/{site_id}/` |
| OAuth install flow | ✅ | `GET /oauth/start` → Webflow → `GET /oauth/callback` → `/oauth/success` |

### WordPress Only

| Feature | Status | Notes |
|---|---|---|
| WordPress plugin architecture | ✅ | Plugin slug: `mpp-store-locator` |
| Shortcode embedding | ✅ | `[mpp_store_locator]` |
| Gutenberg block | ✅ | `mpp/store-locator` — registered via `register_block_type()` |
| WordPress nonce auth | ✅ | `X-WP-Nonce` header; `wp_create_nonce('wp_rest')` |
| MySQL location storage | ✅ | Custom table `wp_mpp_store_locations` |
| `wp_options` settings storage | ✅ | Key: `mpp_store_locator_settings` |
| Batch re-geocode endpoint | ✅ | `POST /admin/locations/geocode-all` — processes 10/call for back-fill |
| Batch directions regeneration | ✅ | `POST /admin/locations/generate-directions` |
| PHP server-side geocoding | ✅ | `wp_remote_get()` to Mapbox API — no Node.js required |

---

## Roadmap Features

Ordered by priority. Implement in sequence unless there is a client-specific reason not to.

### Tier 1 — Highest ROI

#### 1. Structured Hours + Open Now

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | ✅ | DB v1.1 — `schedule` TEXT column; weekly editor in admin; badge + Open now filter in widget |

**What:** Replace free-text `hours` with a structured `schedule` field. Unlocks open-now filtering, opening-soon badges, day-by-day display, holiday hours, and event date/time support.

**Schema addition:**
```json
{
  "schedule": {
    "timezone": "America/Toronto",
    "weekly": {
      "mon": [{ "start": "09:00", "end": "17:00" }]
    },
    "exceptions": [
      { "date": "2026-12-25", "closed": true }
    ]
  }
}
```

**Rules:**
- Keep the existing `hours` text field — backward compatibility.
- Preserve text-based `hours` rendering when `schedule` is absent.
- Widget derives: `is_open_now`, `next_open_time`, `status_label`.
- Add optional filters: `Open now`, `Open today`.

---

#### 2. Categories + Tags + Attributes

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Generic metadata fields for vertical flexibility. Replaces any need for vertical-specific hardcoded fields.

**Schema addition:**
```json
{
  "categories": ["retailer", "service-center"],
  "tags": ["pickup", "wheelchair-accessible"],
  "attributes": {
    "brand": "Brand A",
    "appointment_required": true
  }
}
```

**Rules:**
- Start with `categories` and `tags`; add `attributes` only if needed.
- Admin: manual editing, CSV import mapping, filter chips.
- Widget: category filter, optional badges on cards.
- Never add vertical-specific hardcoded fields — use tags/attributes instead.

---

#### 3. Radius Filter

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Distance-based radius filtering after geolocation or postal-code search.

**Rules:**
- Options: 5 km / 10 km / 25 km / 50 km / 100 km.
- Apply only when `lat/lng` is known.
- Show radius select in widget only after user location is established.
- Keep existing "Near me" button.
- Add `radius_km` query param to public locations endpoint.

---

#### 4. Postal / ZIP Search

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** User-entered postal code or place text → geocoded coordinates → feeds existing distance-based location fetch.

**New endpoint:** `GET /api/geocode-search?q=...` — returns `{ query, latitude, longitude, label }`.

**Rules:**
- Keep text search (location name/address) separate from this origin search input.
- Use Mapbox geocoding for user input.
- Feed geocoded result into the existing distance sort/filter logic.

---

#### 5. Analytics and Event Tracking

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Server-side event ingestion for widget interactions.

**Events to track:** `locator_loaded`, `search_used`, `near_me_used`, `radius_changed`, `filter_applied`, `location_selected`, `directions_clicked`, `cta_clicked`.

**Event schema:**
```json
{
  "event": "cta_clicked",
  "location_id": "abc123",
  "site_id": "xyz",
  "context": { "cta_slot": "cta2", "query": "Toronto", "radius_km": 25 },
  "timestamp": "2026-04-30T12:00:00Z"
}
```

**Rules:**
- Store as append-only log; add GA4/GTM forwarding later as optional integration.

---

### Tier 2 — Strong Product Upgrades

#### 6. Marker Clustering

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Optional map marker clustering for dense datasets.

**Rules:**
- Use native Mapbox source/layer clustering — not individual marker instances.
- Configurable per-widget or globally.
- Clicking a cluster zooms in.
- Preserve custom marker mode for non-clustered use.

---

#### 7. Featured Locations and Sort Rules

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Generic `featured` boolean and `sort_weight` integer for flexible ranking.

**Schema addition:**
```json
{ "featured": false, "sort_weight": 0 }
```

**Default sort logic:**
- No user location: featured first → sort_weight → alphabetic.
- With user location: nearest first (configurable); featured as tie-breaker or override.

---

#### 8. Richer Card Content

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Optional fields for images, badges, and secondary labels on location cards.

**Schema addition:**
```json
{
  "image_url": "",
  "badge_text": "",
  "secondary_text": ""
}
```

**Rules:**
- Keep card layout backward-compatible.
- Add appearance toggles: show image, show badge, show description, show schedule status.

---

#### 9. Data Health Tools

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Admin-side data quality panel surfacing actionable counts.

**Checks:**
- Missing coordinates
- Duplicate address candidates
- Broken CTA URLs
- Empty required fields

**Rules:**
- Deterministic checks only — no fuzzy matching initially.

---

#### 10. Preset Configurations

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Onboarding presets that prefill labels and defaults without forking the schema.

**Presets:** Store Locator · Event Locator · Where to Buy · Dealer Locator · Service Center.

**Rules:**
- Presets only write standard settings values (labels, CTA defaults, filter defaults).
- Do not fork the data schema by preset.
- Users can switch presets without data loss.

---

### Tier 3 — Later, But Valuable

#### 11. Saved Search State + Shareable URLs

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Sync widget state (query, filters, selected location) to URL params for bookmarkable results.

**Rules:** Optional — off by default for embeds where URL mutation is undesirable.

---

#### 12. Localization and Unit Preferences

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**What:** Custom UI labels, km/mi toggle, locale-aware address formatting, multi-language strings.

---

#### 13. External Integrations

| Platform | Status | Notes |
|---|---|---|
| Shopify | 📋 | |
| Webflow | 📋 | |
| WordPress | 📋 | |

**Candidates:** Google Sheets sync · Airtable sync · Webhook push/pull · Public API tokens for external management.

---

## Full Target Data Model (All Platforms)

Current fields plus all roadmap additions:

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

New fields must be optional and default to safe empty values so existing locations continue to render correctly.

---

## Recommended Implementation Order

1. Structured hours + open now
2. Categories + tags
3. Radius filter
4. Postal / ZIP origin search
5. Analytics events
6. Featured locations + sort rules
7. Marker clustering
8. Richer card content
9. Data health tools
10. Preset configurations
