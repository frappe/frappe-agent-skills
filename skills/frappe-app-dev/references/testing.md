# Testing

## Placement

- DocType tests: `apps/<app>/<app>/<module>/doctype/<doctype>/test_<doctype>.py`.
- Feature tests: `apps/<app>/<app>/tests/test_<feature>.py`.

## Rules

- Use Frappe test base classes (`IntegrationTestCase` or `UnitTestCase`) instead of plain `unittest.TestCase` for Frappe-aware tests.
- Run data-mutating tests on a test site, not the user's active development site.
- Always pass `--site` to `bench run-tests`.
- If tests fail with missing DocTypes, run `bench --site <site> migrate` first.

## Commands

```bash
bench --site <site> run-tests --app <app-name>
bench --site <site> run-tests --doctype "DocType Name"
bench --site <site> run-tests --module <module.path> --test <test_name>
```
