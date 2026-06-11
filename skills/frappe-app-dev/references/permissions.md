# Permissions

Permissions must be enforced consistently across Desk, API, background jobs, and direct document methods.

## Rules

- Define baseline DocType permissions in DocType JSON.
- Create custom roles through fixtures, install/setup code, or exported site records; do not invent arbitrary source folders for standard DocTypes.
- Use `ignore_permissions` only in trusted server-side flows, never as a shortcut in user-facing APIs.
- Pair list filtering with document-level checks when access is row-specific.

## Where Logic Goes

- DocType permission table: coarse role permissions.
- Controller `has_permission`: per-document checks.
- `permission_query_conditions` in `hooks.py`: list filtering and `get_list` visibility.
- Business action methods: explicit role/state checks for mutating operations.

## Pitfalls

- `permission_query_conditions` protects lists, not direct document access by itself.
- `get_all` ignores permissions; use `get_list` for permission-aware user-facing queries.
- `frappe.set_user` and `ignore_permissions` can leak privileges if not restored or scoped tightly.
