# Site Management

## Site Selection

- Site directories live under `sites/`.
- Ignore `assets`, `apps.txt`, `common_site_config.json`, and `currentsite.txt`.
- If multiple sites exist, confirm app installation with `bench --site <site> list-apps`.
- Prefer explicit `--site <site>` over relying on `currentsite.txt`.

## Creating Sites

- Prefer `<app-name>.localhost` for local app sites.
- If `root_password` is already in `sites/common_site_config.json`, `bench new-site <site>.localhost --admin-password admin` is enough.
- Otherwise pass `--db-root-password` or ask the user to set global `root_password` once.

## Destructive Commands

Ask before dropping, restoring, or otherwise replacing site data.

```bash
bench drop-site <site> --db-root-password '<pwd>'
bench --site <site> restore <backup-path>
```
