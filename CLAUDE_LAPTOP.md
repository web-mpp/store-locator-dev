# CLAUDE.md — MPP Store Locator (Root)

This is the root-level guidance file for the MPP Store Locator product family.

## What This Repository Contains

Three complete, production-deployed platform implementations of the same store locator product:

| Platform | Folder | Status | Server |
|---|---|---|---|
| Shopify | `mpp-store-locator/` | LIVE (sorbelicious.myshopify.com, 590 locations) | `shopify-store-locator.apps.mppdev.net:3000` |
| Webflow | `mpp-store-locator-webflow/` | LIVE | `webflow-store-locator.apps.mppdev.net:3002` |
| WordPress | `mpp-store-locator-wp/` | DEPLOYED to test.mppdev.net | `test.mppdev.net` |

All three share the same data model, settings schema, widget behavior, and feature set.

**Source of truth for features and roadmap:** `FEATURES.md` (this folder)

---

## How to Work Across Platforms

When implementing any new feature:

1. Extend `FEATURES.md` first — record the feature as planned.
2. Implement in the correct platform folder(s).
3. Update `FEATURES.md` to mark the feature live on each platform as it ships.
4. Keep response shapes, field names, settings keys, and widget behavior identical across all three platforms unless a platform constraint makes that impossible.

Platform-specific differences (auth, hosting, file structure) are acceptable. Product behavior must stay aligned.

---

## Platform Quick Reference

### Shopify — `mpp-store-locator/`

**Full guidance:** `mpp-store-locator/CLAUDE.md`

- Backend: Node.js/Express + `@shopify/shopify-app-express` v7
- Admin UI: React + Vite + Shopify Polaris
- Widget: React IIFE via Shopify theme app extension
- Auth: Shopify OAuth + `FileSessionStorage`
- Data: JSON files at `web/data/shops/{domain}/`
- Map: Mapbox GL JS (public `pk.*` token)
- Local path: `C:\Work\MPP\Apps\store-locator-app\store-locator-app\`
- Dev command: `shopify app dev` from the local path above
- GitHub: `github.com/web-mpp/mpp-store-locator`

### Webflow — `mpp-store-locator-webflow/`

**Full guidance:** `mpp-store-locator-webflow/CLAUDE.md`

- Backend: Node.js/Express (custom, no Shopify libs)
- Admin UI: React + Vite (no Polaris)
- Widget: React IIFE, auto-injected via Webflow Custom Code API
- Auth: Webflow OAuth + per-site HMAC tokens
- Data: JSON files at `server/data/sites/{site_id}/`
- Map: Mapbox GL JS (public `pk.*` token)
- Local path: `C:\IAgent\mpp-store-locator-webflow\`
- GitHub: `github.com/web-mpp/mpp-store-locator-webflow`

### WordPress — `mpp-store-locator-wp/`

**Full guidance:** `mpp-store-locator-wp/CLAUDE.md`

- Backend: PHP WordPress plugin
- Admin UI: React + Vite (no Polaris), mounted in WP admin
- Widget: React IIFE via shortcode `[mpp_store_locator]` or Gutenberg block `mpp/store-locator`
- Auth: WordPress nonces (`X-WP-Nonce` header)
- Data: MySQL table `wp_mpp_store_locations` + `wp_options` for settings
- Map: Mapbox GL JS (public `pk.*` token)
- Geocoding: PHP via `wp_remote_get()` to Mapbox API
- Local path: `C:\IAgent\mpp-store-locator-wp\`
- GitHub: `github.com/web-mpp/mpp-store-locator-wp`

---

## Shared Data Model

All three platforms use this identical location schema:

```
id, name, address, city, province, postal_code, country,
latitude, longitude, phone, email, hours, description,
cta_label, cta_url, cta2_label, cta2_url, cta3_label, cta3_url,
directions_url, directions_show
```

- `latitude`/`longitude` — auto-geocoded via Mapbox on every save/import
- `directions_url` — auto-generated Google Maps URL from address fields
- `directions_show` — boolean; hides per-location directions button when false
- CTA buttons render only when both `label` and `url` are present

**Target schema (roadmap additions — see `FEATURES.md`):**
```json
{
  "schedule": {},
  "categories": [],
  "tags": [],
  "attributes": {},
  "image_url": "",
  "badge_text": "",
  "secondary_text": "",
  "featured": false,
  "sort_weight": 0
}
```

---

## Shared Settings Schema

All three platforms use this identical shape stored as `mapbox_token` + `appearance`:

```json
{
  "mapbox_token": "pk.eyJ1...",
  "appearance": {
    "layout": "split",
    "sidebar_position": "left",
    "sidebar_width": 360,
    "card_layout": "list",
    "font_family": "",
    "header_font_family": "",
    "font_size_base": 15,
    "font_size_heading": 16,
    "font_size_address": 13,
    "font_size_button": 13,
    "marker_color": "#3FB1CE",
    "marker_active_color": "#FF6B35",
    "sidebar_bg": "#ffffff",
    "card_bg": "#ffffff",
    "card_active_bg": "#f0faf6",
    "card_border_color": "transparent",
    "card_active_border": "#008060",
    "card_border_width": 2,
    "card_shadow": false,
    "card_shadow_intensity": "low",
    "cta_color": "#008060",
    "cta_text_color": "#ffffff",
    "cta_border_radius": 4,
    "cta_padding_size": "medium",
    "cta_style": "solid",
    "map_height": 600,
    "map_style": "default",
    "map_style_custom": "",
    "marker_style": "default",
    "marker_icon_url": "",
    "card_padding": 12,
    "card_gap": 8,
    "sidebar_padding": 8,
    "border_radius": 6,
    "mobile_layout": "stack",
    "cta2_color": "#008060",
    "cta2_text_color": "#ffffff",
    "cta2_border_radius": 4,
    "cta2_padding_size": "medium",
    "cta2_style": "outline",
    "cta3_color": "#555555",
    "cta3_text_color": "#ffffff",
    "cta3_border_radius": 4,
    "cta3_padding_size": "medium",
    "cta3_style": "ghost",
    "directions_enabled": true,
    "directions_label": "Get Directions",
    "directions_color": "#1a73e8",
    "directions_text_color": "#ffffff",
    "directions_style": "outline",
    "directions_padding_size": "medium",
    "directions_border_radius": 4,
    "search_enabled": true,
    "filter_city_enabled": true,
    "filter_province_enabled": true
  }
}
```

New appearance fields must be added to `models/settings.js` (Shopify/Webflow) or `class-settings.php` (WordPress) with defaults that auto-merge for existing installations.

---

## Product Principles

1. Keep the product generic — it must work for stores, events, dealers, showrooms, and service centers.
2. Prefer generic field names over vertical-specific names.
3. New fields must be backward-compatible — existing data must not break.
4. Preserve CSV import/export workflows on all platforms.
5. Keep settings keys, field names, and widget behavior identical across Shopify, Webflow, and WordPress.
6. Implement features as composable primitives — avoid hardcoded business logic.

---

## Implementation Sequence for New Features

For every new feature, implement in this order:

1. Update the shared data model (schema + defaults)
2. Update CSV import/export support
3. Update admin forms
4. Update public API response
5. Update widget UI

Do not skip steps. CSV support must ship alongside the admin and API changes.

---

## Production Servers

All on AWS: `35.183.253.194`

| App | PM2 name | Port |
|---|---|---|
| Shopify | `store-locator` | 3000 |
| Webflow | `store-locator-webflow` | 3002 |
| WordPress | n/a (PHP, Apache) | standard |

SSH: `ssh -i ~/.ssh/mppdev_key mppdev@35.183.253.194`
WordPress SSH key: `~/.ssh/mppdev_plesk` (different key)

---

## Feature Roadmap

See `FEATURES.md` for the full feature matrix and roadmap with per-platform status.
