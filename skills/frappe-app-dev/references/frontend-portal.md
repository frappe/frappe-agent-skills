# Portal Pages

Use this for server-rendered website/portal pages with Jinja templates.

## Files

- Template: `apps/<app>/<app>/www/<page>.html`.
- Context: `apps/<app>/<app>/www/<page>.py`.
- Routes and website permissions usually live in `hooks.py`.

## Rules

- Escape by default; avoid marking user content safe unless sanitized.
- Use permission-aware queries for user-facing data.
- Keep page context thin; move reusable logic into app modules.
- Check `has_website_permission` or existing portal permission patterns before exposing document data.
