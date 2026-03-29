# frappe-agent-skills

Agent skill for building full-stack [Frappe Framework](https://frappeframework.com/) applications.

## Install

```bash
npx skills add netchampfaris/frappe-agent-skills --skill frappe-dev
```

## What it covers

- DocTypes, fields, naming, child tables
- Controller lifecycle hooks and validation patterns
- Whitelisted APIs (`@frappe.whitelist()`) and built-in v2 REST APIs
- Database ORM — `frappe.db`, `frappe.qb.get_query`, filters, transactions
- Hooks (`hooks.py`) — doc events, scheduled jobs, fixtures, install hooks
- Permissions — role-based, row-level (`permission_query_conditions`), `has_permission`
- Background jobs (`frappe.enqueue`, `frappe.enqueue_doc`)
- Realtime (Socket.IO, `publish_realtime`)
- Caching (`frappe.cache`)
- Frontend — Desk client scripts, Vue 3 + frappe-ui SPA, portal pages
- Testing — `IntegrationTestCase`, `UnitTestCase`, test site setup
- Bench CLI — app/site lifecycle, migrations, fixtures

## Usage

Once installed, the skill activates automatically when you ask your agent about Frappe development tasks — creating DocTypes, writing controllers, setting up a bench site, adding APIs, and so on.
