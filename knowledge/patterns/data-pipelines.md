# Patterns: Data pipelines

Config-driven Airflow is my day job. These are the properties I want a pipeline
to have, independent of the framework.

## Idempotency is the whole game

**Running the same task twice for the same date must produce the same result as
running it once.** Almost every painful pipeline incident I've seen traces back
to this not being true.

- Replace the partition; don't append to it. `DELETE WHERE date = X` then insert,
  or an atomic overwrite of that partition, or a MERGE on a genuinely unique key.
- Appending is only safe if I'm certain the task will never re-run, and I'm never
  certain of that — retries, backfills, and someone clearing a task all exist.
- Derive the target partition from the logical date, never from "now". A task
  that computes `today()` internally produces a different result when it's
  re-run next week, which makes backfills meaningless.
- Side effects that can't be made idempotent — sending a file, calling an
  external API, notifying someone — need a guard, and they belong in their own
  task so a retry of the compute step doesn't re-fire them.

## Dates

State which date you mean, every time: the **logical/data date** the run is
*about*, versus the **wall-clock time** it happens to execute. They differ by an
interval, they differ again during a backfill, and confusing them is the classic
off-by-one-day bug that nobody notices until month end.

Late-arriving data is a design decision, not an accident: either the window
re-processes a trailing few days, or late rows are dropped. Pick one on purpose
and write it down, because "we silently lose anything more than a day late" is a
thing stakeholders should know.

## Failing loudly

A task that succeeds with wrong output is worse than a task that fails.

- Validate assumptions in the pipeline, not just in review: expected row count
  range, key uniqueness, no unexpected nulls in required columns, referential
  checks against the parent table.
- A check that logs a warning nobody reads is not a check. Decide whether a
  violation should fail the task or just alert — and if it alerts, name who
  actually looks at it.
- Zero rows is suspicious by default. An empty source and a broken source look
  identical downstream unless something asserts the difference.
- Schema drift: an added upstream column is usually harmless, a changed type or
  a removed column usually isn't. Explicit column selection turns "silently
  different data" into "loud failure", which is the trade I want.

## Config-driven specifics

- **The config is the production change.** A one-line YAML edit can add a task,
  change a target table, or alter a window. Review it with the same care as
  code, and in the PR say what it changes about the *expanded* result, not just
  what the diff says.
- Validate config at parse time — schema, required keys, allowed values. A typo'd
  key that silently defaults is a bug that only shows up in the output.
- When debugging, the first question is "what config did this run actually
  load", not "what does the DAG do". Print the resolved config in the mirror
  notebook before anything else ([../20-method.md](../20-method.md)).
- Resist putting logic in the config. Once config has conditionals it's code
  without tests, review tooling, or a debugger.
- Keep the DAG file thin and the transformation in plain functions I can import
  into a notebook. If I have to spin up the scheduler to test a transformation,
  the code is in the wrong place.

## Task design

- One task, one responsibility, at a granularity I'd be happy to retry
  individually. Big tasks mean re-doing expensive work to fix a cheap step.
- Extract, transform and publish as separate steps: if publish fails I want to
  retry the publish, not the whole extract.
- Retries are for transient failures. A retry on a deterministic failure just
  delays the alert three times over.
- Make dependencies real dependencies. Two tasks that happen to run in order
  because of timing will eventually not.

## Backfills

A backfill is bulk mutation of production data — see SELECT before UPDATE in
[../20-method.md](../20-method.md).

- Run one partition first and check the output before running the range.
- Know the blast radius: which downstream tables and reports change, and who
  notices.
- Have the undo plan before starting, and tell people it's happening if the
  numbers they're looking at will move under them.
- Bounded concurrency. A backfill that saturates the warehouse takes production
  down with it.

## Operations

- Alerts should say what broke and what the impact is. "DAG failed" makes
  someone open a UI to learn anything at all.
- Clearing task state and marking success are production mutations. Ask first
  ([../10-collaboration.md](../10-collaboration.md)).
- Secrets from the secret store, never in config or code, and never printed in a
  notebook cell I might commit or paste.
- When a pipeline has been producing wrong numbers, fixing it forward isn't the
  whole job — say how long it was wrong and who used it. That part is an
  escalation, not a code change.

## For an assistant

- Ask which of extract/transform/publish is failing before proposing anything.
- When you suggest a change, tell me what happens if the task runs twice.
- Don't propose clearing, re-running or backfilling as a first move. That's
  mutating production to see what happens.
