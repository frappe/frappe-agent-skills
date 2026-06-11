# Whitelisted APIs

## Placement

- Prefer whitelisted controller methods for actions on a specific document.
- Use module-level functions in a DocType controller for DocType-level behavior.
- Use standalone `api.py` or `api/` modules only for cross-document, aggregate, or non-document behavior.
- Do not wrap a controller method with a standalone API that only fetches the doc and calls the method.

## Security Rules

- Add type hints to whitelisted parameters so Frappe can cast/validate inputs.
- Declare HTTP methods explicitly when supported by the project pattern.
- Use `allow_guest=True` only for intentionally public endpoints, and return the smallest safe payload.
- Keep business permission checks in the document/service layer, not only in the API wrapper.

## Routes

- Classic whitelisted method route: `/api/method/<full.dotted.path>`.
- Document method route: `/api/v2/document/<DocType>/<name>/method/<method>` when the app/project uses Frappe v15 v2 APIs.
- For version-specific v2 routes, verify existing project usage before assuming availability.
