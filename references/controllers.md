# Controllers

Controllers add server-side logic to DocTypes via Python classes.

## File location

```
apps/<app>/<app>/<module>/doctype/<doctype_name>/<doctype_name>.py
```

## Basic controller

```python
import frappe
from frappe.model.document import Document

class Expense(Document):
    def validate(self):
        if self.amount <= 0:
            frappe.throw("Amount must be positive")

    def before_save(self):
        self.total = sum(item.amount for item in self.items)
```

The class name is the DocType name with spaces removed (e.g. "Expense Category" → `ExpenseCategory`).

## Document lifecycle hooks

Called in this order:

### On insert (new document)
1. `before_insert`
2. `before_naming` (before name is set)
3. `autoname` (custom naming logic — `self.name` is set after this)
4. `before_validate`
5. `validate`
6. `before_save`
7. (db insert)
8. `after_insert`
9. `on_update`
10. `after_save`
11. `on_change`

### On update (existing document)
1. `before_validate`
2. `validate`
3. `before_save`
4. (db update)
5. `on_update`
6. `after_save`
7. `on_change`

### On submit (submittable DocTypes)
1. `before_validate`
2. `validate`
3. `before_save`
4. `before_submit`
5. `on_submit`
6. `on_update`
7. `after_save`
8. `on_change`

### On cancel
1. `before_cancel`
2. `on_cancel`
3. `on_change`

### On delete
1. `on_trash`
2. `after_delete`

## Common patterns

### Set defaults before validation
```python
def before_validate(self):
    if not self.currency:
        self.currency = frappe.defaults.get_global_default("currency")
```

### Throw validation errors
```python
frappe.throw("Error message")                    # general error
frappe.throw("Message", frappe.ValidationError)  # with exception type
```

### Access current user
```python
frappe.session.user  # email of logged-in user
```

### Set field values
```python
def before_save(self):
    self.full_name = f"{self.first_name} {self.last_name}"
```

### Interact with other DocTypes
```python
def on_submit(self):
    frappe.get_doc(
        doctype="Notification Log",
        subject=f"Expense {self.name} approved"
    ).insert(ignore_permissions=True)
```

### Access flags
```python
# Set a flag to skip validation in specific cases
doc.flags.ignore_validate = True
doc.save()
```
