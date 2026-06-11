# Hooks

`hooks.py` is app integration, not a dumping ground for business logic.

## Use Hooks For

- App metadata and dependencies.
- Install/uninstall setup.
- Document event hooks when modifying behavior outside a controller is intentional.
- Scheduled jobs.
- Desk includes.
- Standard class or whitelisted method overrides, sparingly.
- Fixtures for app-owned configuration records.
- Website routes, Jinja methods, boot data, and permission query hooks.

## Rules

- Hook values are importable dotted paths.
- Keep hook functions in dedicated modules; keep `hooks.py` declarative.
- Prefer controller methods over `doc_events` for app-owned DocTypes.
- Override standard classes/methods only when extension hooks are insufficient.
- After changing fixtures, run `bench --site <site> export-fixtures --app <app-name>` when the fixture source is site data.
