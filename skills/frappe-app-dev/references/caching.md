# Caching

Use `frappe.cache` for ephemeral or expensive computed state, not durable data.

## Rules

- Pass logical keys; Frappe prefixes keys for the current site.
- Do not manually prefix keys with the database/site name.
- Prefer TTLs for presence, locks, temporary flags, and computed values.
- Store persistent state in DocTypes/database, not Redis.

## Pitfall

`get_keys` can return raw Redis keys with site prefixes. It is fine for counting, but do not compare those raw keys to unprefixed logical keys.
