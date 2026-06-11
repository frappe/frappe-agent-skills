# Bench Commands

Keep bench usage explicit and site-scoped.

## Rules

- Use bare `bench`.
- Pass `--site <site>` for site-bound commands.
- Run `bench start` only in the background, and only if it is not already running.
- Ask before destructive operations: `drop-site`, restore, uninstall, or database writes outside normal app code.
- Do not use help/version discovery as a substitute for reading project structure.

## Commands Worth Remembering

```bash
bench --site <site> list-apps
bench --site <site> install-app <app-name>
bench --site <site> uninstall-app <app-name>
bench --site <site> migrate
bench --site <site> run-tests --app <app-name>
bench --site <site> clear-cache
bench --site <site> backup
bench build --app <app-name>
bench set-config -g <key> <value>
bench --site <site> set-config <key> <value>
```

For `bench new-app`, pipe answers; avoid interactive heredocs and unsupported non-interactive flags:

```bash
printf '<title>\n<description>\n<publisher>\n<email>\n<license>\nN\nN\nN\n' | bench new-app <app-name>
```
