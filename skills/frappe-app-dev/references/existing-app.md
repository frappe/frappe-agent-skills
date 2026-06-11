# Existing App Workflow

Use this for modifying or fixing an app already present in the bench.

## Flow

1. Confirm bench root by checking for `apps/`, `sites/`, and `Procfile`.
2. Locate the app under `apps/` and read its module structure.
3. Confirm the target site has the app installed with `bench --site <site> list-apps`.
4. Enable developer mode if schema/customization changes are needed.
5. Read nearby DocTypes, controllers, hooks, tests, and UI before editing.
6. Run `bench --site <site> migrate` for schema changes, then targeted verification.

## Non-Obvious Rules

- Do not create another app unless explicitly asked.
- Do not install the app on an arbitrary site if multiple sites exist; identify the right one first.
- Prefer the app's existing module and naming conventions over tutorial-style examples.
