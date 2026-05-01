# Common Paths

This file is the first stop for cross-platform changes. Use it before broad repo searches when the same feature likely exists in Shopify, Webflow, and WordPress under different platform-specific folders.

## How To Use This File

1. Pick the change type below.
2. Open the listed files for the affected platform(s).
3. Only search beyond these paths if the change clearly reaches outside the normal shared structure.

Ignore `node_modules/`, build output, and generated bundles unless the task is explicitly about build tooling or deployment artifacts.

## Shared Planning Docs

| Task | Path |
|---|---|
| Shared feature matrix | `FEATURES.md` |
| Shared roadmap notes | `STORE_LOCATOR_NEXT_FEATURES_CLAUDE.md` |
| Root agent guidance | `AGENTS.md` |
| Root Claude guidance | `CLAUDE.md` |

## Platform Entry Docs

| Platform | Primary doc | Secondary doc |
|---|---|---|
| Shopify | `store-locator-shopify/AGENTS.md` | `store-locator-shopify/CLAUDE.md` |
| Webflow | `store-locator-webflow/CLAUDE.md` | `store-locator-webflow/WEBFLOW_DEV_GUIDE.md` |
| WordPress | `store-locator-WP/CLAUDE.md` | none yet |

## Appearance Settings

Use these first for changes to appearance defaults, appearance schema, preview behavior, layout options, typography, colors, card styles, button styles, map presentation, and mobile layout.

| Change area | Shopify | Webflow | WordPress |
|---|---|---|---|
| Appearance defaults / schema | `store-locator-shopify/store-locator-app/web/models/settings.js` | `store-locator-webflow/server/models/settings.js` | `store-locator-WP/mpp-store-locator/includes/class-settings.php` |
| Appearance tab UI | `store-locator-shopify/store-locator-app/web/frontend/src/components/AppearanceTab.jsx` | `store-locator-webflow/admin-ui/src/AppearanceTab.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/AppearanceTab.jsx` |
| Non-appearance settings tab | `store-locator-shopify/store-locator-app/web/frontend/src/components/SettingsTab.jsx` | `store-locator-webflow/admin-ui/src/SettingsTab.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/SettingsTab.jsx` |
| Shared settings form helpers | `store-locator-shopify/store-locator-app/web/frontend/src/components/SettingsForm.jsx` | n/a | n/a |
| Admin shell that mounts tabs | `store-locator-shopify/store-locator-app/web/frontend/src/App.jsx` | `store-locator-webflow/admin-ui/src/App.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/App.jsx` |

## Widget UI And Frontend Design

Use these for storefront rendering, card markup, marker behavior, search/filter UI, layout rendering, and most visual output changes.

| Change area | Shopify | Webflow | WordPress |
|---|---|---|---|
| Main widget component | `store-locator-shopify/store-locator-app/web/frontend/src/components/StoreLocator.jsx` | `store-locator-webflow/widget/src/store-locator.jsx` | `store-locator-WP/mpp-store-locator/widget/src/store-locator.jsx` |
| Main widget CSS | `store-locator-shopify/store-locator-app/web/frontend/src/store-locator.css` | `store-locator-webflow/widget/src/store-locator.css` | `store-locator-WP/mpp-store-locator/widget/src/store-locator.css` |
| Widget bootstrap / entry | `store-locator-shopify/store-locator-app/web/frontend/src/extension-entry.jsx` | `store-locator-webflow/widget/vite.widget.config.js` | `store-locator-WP/mpp-store-locator/widget/vite.widget.config.js` |
| Platform embed wrapper | `store-locator-shopify/store-locator-app/extensions/store-locator/blocks/store-locator.liquid` | `store-locator-webflow/designer-extension/src/App.jsx` | `store-locator-WP/mpp-store-locator/includes/class-block.php` |

## Location Data Model And CRUD

Use these for new location fields, field validation, CRUD behavior, admin list changes, and modal form changes.

| Change area | Shopify | Webflow | WordPress |
|---|---|---|---|
| Location model / persistence | `store-locator-shopify/store-locator-app/web/models/locations.js` | `store-locator-webflow/server/models/locations.js` | `store-locator-WP/mpp-store-locator/includes/class-locations.php` |
| Location list tab | `store-locator-shopify/store-locator-app/web/frontend/src/components/LocationsTab.jsx` | `store-locator-webflow/admin-ui/src/LocationsTab.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/LocationsTab.jsx` |
| Location modal | `store-locator-shopify/store-locator-app/web/frontend/src/components/LocationModal.jsx` | `store-locator-webflow/admin-ui/src/LocationModal.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/LocationModal.jsx` |
| Location form | `store-locator-shopify/store-locator-app/web/frontend/src/components/LocationForm.jsx` | `store-locator-webflow/admin-ui/src/LocationModal.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/LocationModal.jsx` |
| Location table / list rendering | `store-locator-shopify/store-locator-app/web/frontend/src/components/LocationTable.jsx` | `store-locator-webflow/admin-ui/src/LocationsTab.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/LocationsTab.jsx` |

## CSV Import And Export

Use these first for import mapping, export columns, bulk upload flows, and CSV-related schema updates.

| Change area | Shopify | Webflow | WordPress |
|---|---|---|---|
| CSV action UI | `store-locator-shopify/store-locator-app/web/frontend/src/components/CsvActions.jsx` | `store-locator-webflow/admin-ui/src/CsvActions.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/CsvActions.jsx` |
| CSV toolbar / launch point | `store-locator-shopify/store-locator-app/web/frontend/src/components/CsvToolbar.jsx` | `store-locator-webflow/admin-ui/src/LocationsTab.jsx` | `store-locator-WP/mpp-store-locator/admin-ui/src/LocationsTab.jsx` |
| Import/export backend | `store-locator-shopify/store-locator-app/web/routes/admin/locations.js` | `store-locator-webflow/server/routes/admin/locations.js` | `store-locator-WP/mpp-store-locator/includes/class-rest-api.php` |
| Location persistence touched by CSV | `store-locator-shopify/store-locator-app/web/models/locations.js` | `store-locator-webflow/server/models/locations.js` | `store-locator-WP/mpp-store-locator/includes/class-locations.php` |

## Public And Admin API

Use these for response shapes, request validation, settings endpoints, admin save behavior, and public widget fetch payloads.

| Change area | Shopify | Webflow | WordPress |
|---|---|---|---|
| Public settings endpoint | `store-locator-shopify/store-locator-app/web/routes/settings.js` | `store-locator-webflow/server/routes/public/settings.js` | `store-locator-WP/mpp-store-locator/includes/class-rest-api.php` |
| Public locations endpoint | `store-locator-shopify/store-locator-app/web/routes/locations.js` | `store-locator-webflow/server/routes/public/locations.js` | `store-locator-WP/mpp-store-locator/includes/class-rest-api.php` |
| Admin settings endpoint | `store-locator-shopify/store-locator-app/web/routes/admin/settings.js` | `store-locator-webflow/server/routes/admin/settings.js` | `store-locator-WP/mpp-store-locator/includes/class-rest-api.php` |
| Admin locations endpoint | `store-locator-shopify/store-locator-app/web/routes/admin/locations.js` | `store-locator-webflow/server/routes/admin/locations.js` | `store-locator-WP/mpp-store-locator/includes/class-rest-api.php` |
| App server entry | `store-locator-shopify/store-locator-app/web/server.js` | `store-locator-webflow/server/server.js` | `store-locator-WP/mpp-store-locator/mpp-store-locator.php` |

## Geocoding And Address-Derived Fields

Use these when changes affect lat/lng generation, directions URLs, import geocoding, or save-time address enrichment.

| Change area | Shopify | Webflow | WordPress |
|---|---|---|---|
| Geocode helper | `store-locator-shopify/store-locator-app/web/lib/geocode.js` | `store-locator-webflow/server/lib/geocode.js` | `store-locator-WP/mpp-store-locator/includes/class-locations.php` |
| Bulk geocode script | `store-locator-shopify/store-locator-app/web/geocode-locations.js` | n/a | n/a |
| Settings token source | `store-locator-shopify/store-locator-app/web/models/settings.js` | `store-locator-webflow/server/models/settings.js` | `store-locator-WP/mpp-store-locator/includes/class-settings.php` |

## Data Storage

Useful when debugging real saved data shapes or platform storage behavior.

| Platform | Path |
|---|---|
| Shopify sample local data | `store-locator-shopify/store-locator-app/web/data/locations.json` |
| Shopify real tenant data | `store-locator-shopify/store-locator-app/web/data/shops/{domain}/` |
| Webflow sample local data | `store-locator-webflow/server/data/locations.json` |
| Webflow sample settings | `store-locator-webflow/server/data/settings.json` |
| Webflow real tenant data | `store-locator-webflow/server/data/sites/{site_id}/` |
| WordPress settings | `wp_options` via `mpp_store_locator_settings` |
| WordPress locations | `wp_mpp_store_locations` table |

## Platform-Specific Integration Files

These are the next places to check when a change is not purely shared product logic.

| Platform | Purpose | Path |
|---|---|---|
| Shopify | Theme app extension asset styling | `store-locator-shopify/store-locator-app/extensions/store-locator/assets/store-locator-frontend.css` |
| Shopify | Extension metadata | `store-locator-shopify/store-locator-app/extensions/store-locator/shopify.extension.toml` |
| Webflow | Designer extension manifest | `store-locator-webflow/designer-extension/webflow.json` |
| Webflow | Webflow API helper | `store-locator-webflow/server/lib/webflow.js` |
| WordPress | Plugin bootstrap | `store-locator-WP/mpp-store-locator/mpp-store-locator.php` |
| WordPress | Admin integration | `store-locator-WP/mpp-store-locator/includes/class-admin.php` |

## First-Look Shortcuts By Task

| If you need to change... | Open these first |
|---|---|
| A new appearance field across all platforms | `web/models/settings.js`, `server/models/settings.js`, `includes/class-settings.php`, then each `AppearanceTab.jsx`, then each widget file |
| A new location field across all platforms | each location model file, each CSV UI file, each admin locations endpoint, each location modal/list file, then each widget file |
| CSV import/export behavior | each `CsvActions.jsx`, each admin locations endpoint, each location model file |
| Storefront card layout or visual design | each widget component file plus each widget CSS file |
| Public API response shape | each public `settings` or `locations` route, plus widget fetch/render files |
| Admin save behavior | each admin route plus each settings or appearance tab |
