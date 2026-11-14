# Framework facts

Facts verified against official documentation on the date shown, unless a line is tagged unverified. Re-verify against the project's lockfile versions before relying on a flag. A JavaScript section is added once M names the frontend runner.

## pytest (verified 2026-10-24 against pytest-django docs; pytest flags are standard)

- Run one test by node id: `pytest path/to/test_file.py::test_name -q`. Add `-x` to stop at the first failure, `-rA` to print the summary for every test.
- The runner's summary line (`1 passed in 0.12s`, `1 failed, 3 passed`) and the exit code (`0` pass, `1` failures, `2` interrupted, `4` usage error, `5` no tests collected) are what the `VERIFY:` line quotes. Exit `5` is not a pass.
- When pytest is not installed in the active interpreter, `uv run --with pytest pytest ...` runs it in a throwaway environment; the project's own environment is still the right place for the real run [verified: `uv` present on this machine 2026-10-24; pytest absent from the pyenv Python].

## pytest-django

Source: https://pytest-django.readthedocs.io/en/stable/helpers.html

- A test or fixture that touches the database must request `db`, `transactional_db`, `django_db_reset_sequences`, or `django_db_serialized_rollback`, or carry `@pytest.mark.django_db`. Fixtures must request the fixture explicitly; the marker may not help inside a fixture because of fixture ordering.
- `db`: each test runs inside a transaction that is rolled back at the end (same as `django.test.TestCase`). Fast; the default.
- `transactional_db` (or `@pytest.mark.django_db(transaction=True)`): real transactions, so code that commits or relies on `on_commit` callbacks runs as in production. Slower; use only when the behavior needs it.
- `django_db_reset_sequences`: on top of `transactional_db`, resets auto-increment sequences, where the database supports it.
- Settings: the test run must point at local settings. Before the first run, print the active `DATABASES['default']['HOST']` (or the connection URL) and confirm it is local or a CI service. A production host is a stop.

## Time and randomness in Python

- Inject the clock or freeze it in the test. `freezegun` and `time-machine` are the common libraries [unverified: which one the project uses; check the lockfile].
- Seed `random` or inject a `Random` instance; Hypothesis manages its own seeding and replays failures from its database (https://hypothesis.readthedocs.io/en/latest/tutorial/replaying-failures.html).

## JavaScript

Not yet documented. Ask M which runner the frontend uses before adding facts here.
