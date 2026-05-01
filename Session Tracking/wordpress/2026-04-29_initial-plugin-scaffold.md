# Session: Initial WordPress Plugin Scaffold
Date: 2026-04-29
Platform: WordPress

## What Was Done

Created the `store-locator-WP/` directory under the master `store-locator-app/` folder and built the complete WordPress plugin scaffold from scratch, porting all features from the Shopify and Webflow versions.

**New files created (24 total):**

PHP backend:
- `mpp-store-locator.php` — plugin entry point, registers activation hook and bootstraps all classes
- `uninstall.php` — drops custom table and options on plugin deletion
- `includes/class-db.php` — creates `{prefix}mpp_store_locations` via `dbDelta` on activation
- `includes/class-locations.php` — full CRUD, bulk delete, replace/append/upsert import modes, Mapbox geocoding via `wp_remote_get`, haversine distance sort, directions URL auto-generation
- `includes/class-settings.php` — 40+ appearance defaults stored in `wp_options` with deep merge on save
- `includes/class-rest-api.php` — all 11 REST endpoints under namespace `mpp-store-locator/v1`
- `includes/class-admin.php` — WP admin menu page, Vite manifest-aware asset loading, `MPP_SL_CONFIG` injection
- `includes/class-block.php` — Gutenberg block + `[mpp_store_locator]` shortcode, Mapbox CDN load, widget config injection

React admin UI (`admin-ui/src/`):
- `main.jsx`, `App.jsx`, `LocationsTab.jsx`, `LocationModal.jsx`, `CsvActions.jsx`, `AppearanceTab.jsx`, `SettingsTab.jsx`, `admin.css`
- `package.json`, `vite.config.js`, `index.html`

React widget (`widget/src/`):
- `store-locator.jsx`, `store-locator.css`
- `package.json`, `vite.widget.config.js`

Gutenberg editor block:
- `assets/block-editor.js` — editor-side preview block registration

Updated files:
- `store-locator-WP/CLAUDE.md` — generated at session start, updated with completed scaffold status
- `store-locator-app/CLAUDE.md` (master) — added WordPress to platform table and shared concepts

## Key Decisions

**PHP instead of Node.js for the backend.** The Shopify and Webflow versions both use Express + JSON file storage. The WordPress version uses PHP with a custom DB table (`wp_`) and `wp_options`, which is the correct WordPress pattern and avoids running a separate server process.

**No separate auth layer.** Shopify uses App Bridge session tokens; Webflow uses OAuth + HMAC tokens. The WordPress version uses WordPress's own authentication — `manage_options` capability check on admin REST endpoints, with a WP nonce (`wp_create_nonce('wp_rest')`) injected by PHP and sent as `X-WP-Nonce`. No login gate in the React app.

**Vite manifest for asset loading.** Rather than hardcoding JS/CSS filenames (which Vite hashes on build), `class-admin.php` reads `.vite/manifest.json` to find the hashed entry point at runtime.

**Admin CSS scoped under `#mpp-sl-admin-root`.** WordPress admin already has a lot of global styles. All admin UI CSS was prefixed under `#mpp-sl-admin-root` to prevent conflicts with WP's own admin styles.

**Mapbox GL JS loaded from CDN by the block/shortcode.** Same approach as Webflow — the widget IIFE marks `mapbox-gl` as external and expects `window.mapboxgl` to be present. `class-block.php` enqueues the CDN script before the widget IIFE.

**Upsert matching key.** For CSV upsert mode, rows are matched by `address + postal_code` (same as the Webflow/Express version). This is a reasonable uniqueness proxy for physical locations.

**`directions_show` as TINYINT(1).** In PHP, the value comes out of MySQL as `"0"` or `"1"` (string). `cast_row()` in `class-locations.php` explicitly casts it to a PHP bool so the REST API response matches the shape the React widget and admin expect (`directions_show !== false`).

## Files Changed

| File | Change |
|---|---|
| `store-locator-WP/CLAUDE.md` | Created — full context doc for WP platform |
| `store-locator-WP/mpp-store-locator/mpp-store-locator.php` | Created — plugin entry |
| `store-locator-WP/mpp-store-locator/uninstall.php` | Created |
| `store-locator-WP/mpp-store-locator/includes/class-db.php` | Created |
| `store-locator-WP/mpp-store-locator/includes/class-locations.php` | Created |
| `store-locator-WP/mpp-store-locator/includes/class-settings.php` | Created |
| `store-locator-WP/mpp-store-locator/includes/class-rest-api.php` | Created |
| `store-locator-WP/mpp-store-locator/includes/class-admin.php` | Created |
| `store-locator-WP/mpp-store-locator/includes/class-block.php` | Created |
| `store-locator-WP/mpp-store-locator/admin-ui/**` | Created (8 src files + package.json + vite.config.js + index.html) |
| `store-locator-WP/mpp-store-locator/widget/**` | Created (2 src files + package.json + vite.widget.config.js) |
| `store-locator-WP/mpp-store-locator/assets/block-editor.js` | Created |
| `store-locator-app/CLAUDE.md` | Updated — added WordPress row to platform table and extra docs table; updated "Shared Concepts" |

## Gotchas / Fixes

**`directions_show` bool casting.** MySQL returns `TINYINT` as a string from `$wpdb->get_results()`. Without explicit casting in `cast_row()`, `directions_show` would come out as `"1"` or `"0"` (string), causing the widget's `loc.directions_show !== false` check to always pass — even for rows where it was set to `0`. Added explicit `(bool)` cast.

**Vite manifest path.** Vite 5 writes the manifest to `dist/.vite/manifest.json` (not `dist/manifest.json` as in older versions). `class-admin.php` reads from the `.vite/` subdirectory.

**`dbDelta` column syntax.** `dbDelta` is strict about SQL formatting — it requires two spaces between the column definition and any constraints, and `PRIMARY KEY` must be on its own line. Deviating from this causes silent no-ops where the table schema doesn't update.

**WP admin CSS conflicts.** WordPress admin injects a large reset and utility stylesheet. Without scoping all admin UI styles under `#mpp-sl-admin-root`, WP's styles for `input`, `select`, `button`, and `table` bleed into the React app. All selectors in `admin.css` were prefixed accordingly.

**`wp_remote_get` timeout.** Default WP HTTP timeout is 5 seconds. Mapbox geocoding occasionally exceeds this on cold requests. Set `timeout: 10` in the `wp_remote_get` args in `class-locations.php`.

**Admin Vite `base` path.** Must exactly match the URL WordPress serves the plugin from: `/wp-content/plugins/mpp-store-locator/admin-ui/dist/`. If this is wrong, Vite's asset references will 404. This is the most likely thing to need adjustment if the WP install is in a subdirectory.

## Next Steps

1. `npm install && npm run build` in both `admin-ui/` and `widget/` directories
2. Deploy plugin folder to `test.mppdev.net` (`wp-content/plugins/mpp-store-locator/`)
3. Activate plugin in WP admin and confirm DB table is created
4. Enter a Mapbox public token in the Settings tab
5. Add a few test locations and verify geocoding works via `wp_remote_get` on the server
6. Test `[mpp_store_locator]` shortcode on a page — confirm map renders with token and locations
7. Test CSV import (replace + upsert modes) with a sample file
8. Verify Gutenberg block appears in block inserter and renders correctly on front end
9. Confirm admin nonce auth — REST calls should return 401 when nonce is missing/expired
10. Update CLAUDE.md with real server plugin path once deployed
