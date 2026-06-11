# Controllers

Use controllers for DocType behavior that must hold across Desk, API, background jobs, imports, and tests.

## Rules

- Put document behavior in `apps/<app>/<app>/<module>/doctype/<doctype>/<doctype>.py`.
- The controller class name is the DocType name with spaces removed.
- Prefer controller methods for state transitions; do not hide document mutations in unrelated API wrappers.
- Put permission checks inside mutating controller methods when the rule is business-specific.
- Avoid `frappe.db.set_value` for validated fields or state transitions because it skips controller hooks.
- Do not call `frappe.db.commit()` inside controller methods.

## Lifecycle Hooks Worth Remembering

- New document: `before_insert`, `before_naming`, `autoname`, `before_validate`, `validate`, `before_save`, `after_insert`, `on_update`, `after_save`, `on_change`.
- Existing document: `before_validate`, `validate`, `before_save`, `on_update`, `after_save`, `on_change`.
- Submit/cancel/delete: `before_submit`, `on_submit`, `before_cancel`, `on_cancel`, `on_trash`, `after_delete`.

Use `before_validate` for defaults, `validate` for invariants, and explicit methods for user/business actions.
