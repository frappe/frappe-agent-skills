# Realtime

Frappe realtime events route through Socket.IO rooms backed by Redis pub/sub.

## Publishing Rules

- Room resolution priority: explicit `room` > `task_id` > `user` > `doctype` + `docname` > site room.
- Use `after_commit=True` for events emitted during document saves or request transactions.
- Use `user=` for private notifications.
- Use `doctype=` + `docname=` for document-specific updates.
- Avoid accidental site-wide broadcasts by omitting all routing arguments.

## Rooms

- Site room: all connected Desk users on the site.
- User room: one user's active connections.
- Doc room: users viewing a specific document.
- Doctype room: list/update subscribers.
- Task room: background task tracking.

Portal or guest realtime behavior should be verified against the app's existing socket/auth setup.
