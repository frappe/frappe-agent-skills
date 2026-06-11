# Database & ORM

## Query Choice

- Use `frappe.get_doc` / `doc.save()` when controller validation and hooks must run.
- Use `frappe.db.get_value`, `get_all`, or `get_list` for simple reads.
- `get_all` ignores permissions; `get_list` respects permissions. Use `get_list` for user-facing APIs unless bypassing permissions is intentional.
- Use `frappe.qb.get_query` for joins, aggregations, OR conditions, child-table queries, subqueries, locking, or composable queries.
- Maintain existing lower-level `frappe.qb.DocType` style when editing code that already uses it.

## Writes

- Avoid `frappe.db.set_value` for fields with validation, status changes, or side effects.
- Prefer document methods for state transitions.
- Use `frappe.db.delete` only when controller trash hooks do not need to run.

## Transactions

- Frappe auto-commits successful POST/PUT requests, background jobs, scheduled jobs, and patches.
- Uncaught exceptions roll back automatically.
- Do not call `frappe.db.commit()` in request handlers or controllers unless a later operation in the same flow must see committed data.
- Use savepoints for partial rollback inside a larger transaction.

## Performance Pitfalls

- Batch-fetch related records instead of querying in loops.
- Prefer one query with OR/joins over chained fallback queries when correctness allows it.
- Use unbuffered iteration for large result sets.
