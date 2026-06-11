# Vue 3 + frappe-ui + Vite

Use this for standalone SPAs under an app's `frontend/` directory.

## Detect

- `apps/<app>/frontend/`
- `vite.config.*`
- `frappe-ui`, Vue, or route setup in `frontend/src/`

## Rules

- Preserve the app's existing `frappe-ui` patterns, route structure, and data-fetching composables.
- Ensure `hooks.py` or existing route config serves the SPA route.
- Run package commands from the `frontend/` workdir rather than changing global bench state.
- `bench start` must be running for proxied API calls during development.
- Build with the app's package script or `bench build --app <app-name>` depending on existing convention.

## API Calls

- Prefer the app's existing `frappe-ui` resources/composables.
- Use full dotted method paths for standalone whitelisted APIs.
- Use document resources for normal DocType CRUD when the app already uses them.
