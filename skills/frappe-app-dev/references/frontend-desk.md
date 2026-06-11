# Desk UI

Desk already generates forms and lists from DocTypes. Add client code only for behavior the generated UI cannot express.

## Files

- Form script: `apps/<app>/<app>/<module>/doctype/<doctype>/<doctype>.js`.
- List script: follow existing app conventions for `<doctype>_list.js` when present.

## Rules

- Prefer server/controller validation for business invariants; client validation is UX only.
- Use `frm.call("method")` for whitelisted document methods.
- Use `frappe.call` with full dotted paths for standalone methods.
- Keep child-table recalculation logic local and re-run it on row add/remove/change paths.
- Do not customize Desk UI when fields, permissions, or standard DocType metadata solve it.
