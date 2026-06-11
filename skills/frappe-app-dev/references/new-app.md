# New App Workflow

Use only when the user explicitly wants a new Frappe app.

## Flow

1. Confirm bench root by checking for `apps/`, `sites/`, and `Procfile`.
2. Enable developer mode: `bench set-config -g developer_mode 1`.
3. Pick or create a site — see [site-management.md](./site-management.md).
4. Create the app with piped `bench new-app` input.
5. Install it: `bench --site <site> install-app <app-name>`.
6. Build the requested feature in the generated app module.
7. Run `bench --site <site> migrate`, then targeted verification.

## Non-Obvious Rules

- Do not run bare `bench new-app <name>`; pipe the prompt answers.
- Do not invent a second app when the user likely means an existing app.
- After a successful migrate, do not query the database just to prove schema exists; migrate is the schema verification step.
