# Logging

Frappe provides two logging mechanisms: **file-based server logs** via `frappe.logger()` and **database-stored logs** via `frappe.log_error()`.

## `frappe.logger()` — File-based logging

Returns a Python `logging.Logger` writing to rotating log files at bench-level (`logs/{module}.log`) and site-level (`sites/{site}/logs/{module}.log`).

```python
logger = frappe.logger("myapp")
logger.info("Expense created: %s", self.name)
logger.warning("Amount exceeds threshold: %s", self.amount)
logger.error("Failed to sync: %s", str(e), exc_info=True)
```

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `module` | str | `"frappe"` | Logger name and log filename (`{module}.log`) |
| `with_more_info` | bool | `False` | Appends `Site:` and sanitized `Form Dict:` to every record |
| `allow_site` | str \| bool | `True` | `True` = auto-detect current site; pass site name string to target a specific site; `False` = bench-level only |
| `filter` | callable | `None` | Custom filter function added to the logger |
| `max_size` | int | `100_000` | Max bytes per log file before rotation |
| `file_count` | int | `20` | Number of rotated files to retain |

### Log levels

Default level is `WARNING` in dev, `ERROR` in production. Set it dynamically without restart:

```python
frappe.utils.logger.set_log_level("INFO")   # ERROR, WARNING, INFO, DEBUG
```

Or in site config (for web request logs):
```json
{ "enable_frappe_logger": true }
```

### Logger caching

Loggers are cached in `frappe.loggers` by name `"{module}-{site}"`. Calling `frappe.logger("myapp")` twice returns the same instance — no duplicate handlers.

### Sensitive field redaction

`with_more_info=True` appends the request Form Dict to every log record, but keys containing `password`, `passwd`, `secret`, `token`, `key`, or `pwd` are automatically redacted to `"********"`.

```python
# Use with_more_info=True in request handlers where you want full context
logger = frappe.logger("myapp", with_more_info=True)
```

### Log file locations

```
{bench}/logs/{module}.log               ← bench-level (always written)
{bench}/sites/{site}/logs/{module}.log  ← site-level (written when site is known)
```

---

## `frappe.log_error()` — Database error log

Inserts an `Error Log` document into the database. Visible from Desk → Error Log. Use for errors you want to track, query, or link to specific documents.

```python
# Inside an except block — auto-captures current traceback
try:
    process_expense(self)
except Exception:
    frappe.log_error("Expense processing failed", frappe.get_traceback())
```

### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `title` | str | Short description of the error (single line) |
| `message` | str | Full traceback or error body. Omit to auto-capture via `frappe.get_traceback(with_context=True)` |
| `reference_doctype` | str | DocType to link the error to |
| `reference_name` | str | Document name to link the error to |
| `defer_insert` | bool (kwarg) | Use `deferred_insert()` instead of immediate DB insert |

### Link to a document

```python
frappe.log_error(
    title="Sync failed",
    message=frappe.get_traceback(),
    reference_doctype="Expense",
    reference_name=self.name,
)
```

### Defer insert (read-only or high-frequency contexts)

```python
frappe.log_error("Background task failed", defer_insert=True)
```

Use `defer_insert=True` in background jobs or read-only contexts where an immediate DB write may fail or cause contention.

---

## Desk Logs (built-in DocTypes)

Frappe stores structured events in dedicated DocTypes accessible from Desk:

| DocType | What it tracks |
|---------|---------------|
| `Error Log` | Uncaught exceptions and `frappe.log_error()` calls |
| `Error Snapshot` | HTTP 500+ errors with full stack frames and local variables. Stored as JSON in `sites/{site}/error-snapshots/`. Retained for 1 month by default. |
| `Scheduled Job Log` | Scheduler run history |
| `Activity Log` | User activity events |
| `Access Log` | Document access events |

Query Error Logs programmatically:

```python
errors = frappe.db.get_all(
    "Error Log",
    filters={"reference_doctype": "Expense", "reference_name": self.name},
    fields=["name", "error", "creation"],
    order_by="creation desc",
    limit=10,
)
```

---

## Log retention / auto-cleanup

Configure retention from Desk → Log Settings. To register a custom logging DocType for auto-cleanup:

```python
class MyEventLog(Document):
    @staticmethod
    def clear_old_logs(days=180):
        from frappe.query_builder import Interval
        from frappe.query_builder.functions import Now

        table = frappe.qb.DocType("My Event Log")
        frappe.db.delete(table, filters=(table.modified < (Now() - Interval(days=days))))
```

---

## When to use what

| Situation | Use |
|-----------|-----|
| Debugging, tracing execution flow | `frappe.logger("myapp").info(...)` |
| Catching and storing exceptions for ops visibility | `frappe.log_error(title, frappe.get_traceback())` |
| Linking an error to a specific document | `frappe.log_error(..., reference_doctype=..., reference_name=...)` |
| High-frequency or read-only context errors | `frappe.log_error(..., defer_insert=True)` |
| Development-only debug output | `frappe.log(msg)` — writes to stderr + `frappe.debug_log` |

---

## Anti-patterns

- **Don't use `frappe.log_error()` for informational events.** It creates Error Log records — reserve it for actual errors caught in `except` blocks.
- **Don't call `frappe.logger()` on every request without caching.** Loggers are cached by name automatically; always pass a consistent `module` name so the cache is hit.
- **Don't log raw `frappe.form_dict` manually.** Use `with_more_info=True` instead — it handles sanitization of sensitive fields automatically.
- **Don't rely on `frappe.log(msg)` in production.** It writes to stderr only and is not persisted anywhere.
