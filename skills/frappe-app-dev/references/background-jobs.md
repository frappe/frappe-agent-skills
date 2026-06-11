# Background Jobs

Use background jobs for work that should not block a request or needs worker isolation.

## Rules

- Enqueued functions must be importable by dotted path.
- Pass data through arguments; workers do not share request-local state.
- Use `enqueue_after_commit=True` when the job depends on data written in the current transaction.
- Pick queue by expected runtime: `short`, `default`, or `long`.
- Prefer `frappe.enqueue_doc` when the job is naturally a method on a document.

## Scheduler

Define scheduled jobs in `hooks.py` under `scheduler_events`; keep the actual work in importable functions.

## Pitfalls

- Do not enqueue a job that reads uncommitted data unless it is delayed until after commit.
- Make jobs idempotent when retries or duplicate scheduling are possible.
- Ensure site context exists before using `frappe.db` in worker code.
