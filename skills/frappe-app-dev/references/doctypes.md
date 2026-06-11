# DocTypes

DocTypes are schema, permissions, generated UI, and controller wiring. Treat changes as migrations, not just JSON edits.

## File Layout

```text
apps/<app>/<app>/<module>/doctype/<doctype_name>/
  <doctype_name>.json
  <doctype_name>.py
  <doctype_name>.js
  __init__.py
```

## Rules

- Prefer existing DocType JSON patterns in the app over hand-written tutorial examples.
- If writing a DocType manually, create the full folder/file pair intentionally; `bench migrate` applies schema but cannot create a missing source folder from nothing.
- Use snake_case folder/file names for DocTypes; class names remove spaces, e.g. `Expense Category` -> `ExpenseCategory`.
- Do not add standard fields such as `creation`, `modified`, `owner`, `modified_by`, or `docstatus`.
- Child tables need `"istable": 1` and normally no independent permissions.
- Submittable DocTypes use `docstatus`: `0` draft, `1` submitted, `2` cancelled.
- Run `bench --site <site> migrate` after schema changes.

## Watch For

- Fieldname changes can be data migrations, not simple renames.
- Permission changes affect API/list behavior, not just Desk UI.
- Link and Table `options` must be the target DocType name, not the Python module path.
