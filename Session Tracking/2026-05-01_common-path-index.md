# Session: Common path index for cross-platform edits
Date: 2026-05-01
Platform: Shopify, Webflow, WordPress (shared root workflow)

## What Was Done

Added a new root reference file, `COMMON_PATHS.md`, to reduce search time for global changes across the store locator platforms. The file groups the most commonly edited paths by task, including appearance settings, widget UI, CSV import/export, location schema and CRUD, API routes, geocoding, data storage, and platform-specific integration files.

Updated the root `AGENTS.md` and `CLAUDE.md` so future sessions check `COMMON_PATHS.md` before broad repo searches. Also corrected the root guidance to point to the platform docs that actually exist today: Shopify uses `store-locator-shopify/AGENTS.md`, while Webflow and WordPress use their platform `CLAUDE.md` files.

## Key Decisions

- Used a single root-level reference doc instead of duplicating the same path map into multiple platform docs.
- Organized the reference by change type rather than by platform alone, because most global work starts from a task like "appearance settings" or "CSV import" rather than from one codebase.
- Kept generated output and `node_modules` out of the recommended search paths so the guide stays fast and high-signal.

## Files Changed

- `COMMON_PATHS.md`
  Added the shared path index for frequent cross-platform edit targets.
- `AGENTS.md`
  Added instructions to consult `COMMON_PATHS.md` first for shared changes and corrected platform guidance references.
- `CLAUDE.md`
  Added the same shortcut guidance and corrected platform guidance references.

## Gotchas / Fixes

- The repo root had guidance text that referenced platform docs that do not exist for Webflow and WordPress (`AGENTS.md`), so the new shortcut work also cleaned those references up to avoid future dead ends.
- `AGENTS.md` exists in the workspace root but was not yet tracked in git at the time of this session.

## Next Steps

- Expand `COMMON_PATHS.md` when new recurring edit hotspots appear.
- If Webflow or WordPress later gain dedicated `AGENTS.md` files, update the root docs and `COMMON_PATHS.md` to reflect that.
