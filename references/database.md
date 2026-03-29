# Database & ORM

## Reading data

```python
# Get single document
doc = frappe.get_doc("Expense", "EXP-0001")

# Get single value
amount = frappe.db.get_value("Expense", "EXP-0001", "amount")

# Get multiple fields
name, amount = frappe.db.get_value("Expense", "EXP-0001", ["name", "amount"])

# Get all matching records
expenses = frappe.db.get_all("Expense",
    filters={"status": "Draft"},
    fields=["name", "title", "amount", "link_field.field"],
    order_by="creation desc",
    limit=20
)

# Get list (same as get_all but respects permissions)
expenses = frappe.db.get_list("Expense", filters={"status": "Draft"}, fields=["*"])

# Count
count = frappe.db.count("Expense", {"status": "Draft"})

# Check existence
exists = frappe.db.exists("Expense", "EXP-0001")
exists = frappe.db.exists("Expense", {"title": "Lunch", "status": "Draft"})
```

## `get_all` vs `get_list`

- `frappe.db.get_all` — ignores permissions, returns all matching records
- `frappe.db.get_list` — respects user permissions, applies filters based on role

Use `get_all` for server-side logic. Use `get_list` in user-facing APIs.

## Writing data

```python
# Create
doc = frappe.get_doc({"doctype": "Expense", "title": "Lunch", "amount": 50})
doc.insert()

# Update via doc
doc = frappe.get_doc("Expense", "EXP-0001")
doc.amount = 75
doc.save()

# Quick update (single field, no controller hooks)
frappe.db.set_value("Expense", "EXP-0001", "status", "Approved")

# Bulk update
frappe.db.set_value("Expense", {"status": "Draft"}, "status", "Cancelled")
```

## Filters syntax

```python
# Dict style (simple equality)
filters = {"status": "Draft", "amount": 100}

# List style (operators)
filters = [
    ["status", "=", "Draft"],
    ["amount", ">", 50],
    ["title", "like", "%lunch%"],
    ["creation", "between", ["2024-01-01", "2024-12-31"]]
]

# Supported operators: =, !=, >, <, >=, <=, like, not like, in, not in, between, is (for NULL)
```

## `frappe.qb.get_query` (preferred for complex queries)

Use instead of `get_all` when you need: joins via linked/child fields, aggregations, OR conditions, subqueries, or record locking. Docs: https://docs.frappe.io/framework/get_query

```python
# Basic usage
query = frappe.qb.get_query("User", fields=["name", "email"], filters={"enabled": 1})
users = query.run(as_dict=True)

# Linked document fields (auto-joins via dot notation)
query = frappe.qb.get_query("Sales Order",
    fields=["name", "customer.customer_name as customer_name"],
    filters={"customer.territory": "North America"}
)

# Child table fields
query = frappe.qb.get_query("Sales Order",
    fields=["name", {"items": ["item_code", "qty", "rate"]}],
    filters={"items.item_code": "Item A"},
    distinct=True
)

# Aggregations
query = frappe.qb.get_query("Expense",
    fields=["category", {"SUM": "amount", "as": "total"}],
    group_by="category"
)

# OR conditions
query = frappe.qb.get_query("User", filters=[
    ["first_name", "=", "Admin"],
    "or",
    ["first_name", "=", "Guest"],
])

# Pagination
query = frappe.qb.get_query("User", fields=["name"], limit=20, offset=40)

# Permission-aware (default is ignore_permissions=True)
query = frappe.qb.get_query("Expense", ignore_permissions=False)

# Record locking
query = frappe.qb.get_query("Stock Entry", filters={"name": "SE-001"}, for_update=True)

# Large datasets — iterate without loading all into memory
with frappe.db.unbuffered_cursor():
    for row in query.run(as_iterator=True, as_dict=True):
        process(row)
```

### When to use what

| Need | Use |
|------|-----|
| Simple CRUD, single doc | `frappe.get_doc`, `frappe.db.get_value`, `frappe.db.set_value` |
| List with simple filters | `frappe.db.get_all` / `frappe.db.get_list` |
| Joins, aggregations, OR logic, child table queries | `frappe.qb.get_query` |
| Composable query — pass query object to other functions to add clauses | `frappe.qb.get_query` |

## Transactions

Frappe auto-commits after each web request. Manual commit/rollback is rarely needed.

```python
frappe.db.commit()    # almost never needed — only for long-running scripts or background jobs

# Use savepoints for partial rollback within a transaction
frappe.db.savepoint("before_risky_op")
try:
    # risky operation
    ...
except Exception:
    frappe.db.rollback(save_point="before_risky_op")  # rolls back only to savepoint, not entire transaction
```

## PyPika query builder (`frappe.qb`)

You may encounter `frappe.qb.DocType("...")` in existing codebases — this is the lower-level PyPika builder. Prefer `frappe.qb.get_query` (documented above) for new code, but recognize and maintain this style when editing existing code:

```python
Expense = frappe.qb.DocType("Expense")
query = (
    frappe.qb.from_(Expense)
    .select(Expense.name, Expense.amount)
    .where(Expense.status == "Draft")
    .orderby(Expense.creation, order=frappe.qb.desc)
    .limit(20)
)
results = query.run(as_dict=True)
```
