---
name: RDTech cloud sync
description: How cross-browser persistence works via the API server
---

The API server exposes `/api/rdtech/data` (GET/POST) and stores JSON to `/tmp/rdtech_server_data.json`.

Frontend in `storage.ts`:
- `loadFromServer()` — called on mount in historico.tsx; merges server history entries that don't exist in localStorage (server wins for unknown IDs)
- `syncHistoryToServer()` — debounced 800ms; called after every write
- `syncSettingsToServer()` and `syncCredentialsToServer()` — called from admin.tsx on save

**Why:** localStorage is per-browser. The API server provides a shared JSON store so history survives across browsers/devices on the same deployment.

**How to apply:** Every write to localStorage must also call the appropriate sync function. Never forget to call `refresh()` after `loadFromServer()` resolves.
