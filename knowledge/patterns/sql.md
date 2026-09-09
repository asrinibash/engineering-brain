# Patterns: SQL

Craft notes. The hard rule — SELECT before UPDATE — lives in
[../20-method.md](../20-method.md) and isn't repeated here.

## Reading a query I didn't write

Before I trust it, I want to know:

- **What's the grain?** One row per what? If I can't say it in a sentence, I
  don't understand the query yet.
- **Which joins can fan out?** Every join is a potential row multiplier. A join
  that's supposed to be many-to-one and isn't is the single most common cause of
  numbers being quietly too big.
- **Where do the filters live?** A predicate in the `ON` clause of a LEFT JOIN
  and the same predicate in `WHERE` are different queries. The `WHERE` version
  silently turns the LEFT JOIN into an INNER JOIN.
- **What happens to NULLs?** `NOT IN` with a NULL in the list returns nothing.
  `<>` doesn't match NULLs. `COUNT(col)` skips them and `COUNT(*)` doesn't.
  Aggregates ignore them, so an average can be over fewer rows than I think.

## Checking a query means what I think

- Run the count. Then run the count of the distinct key. If they differ, the
  grain isn't what I said it was.
- Join checks: count the driving table, then count after the join. Same number
  means one-to-one held. More means fan-out. Fewer means an INNER JOIN dropped
  rows I probably wanted to know about.
- For a rewrite, don't eyeball it — `EXCEPT`/`MINUS` both directions against the
  original, or full-outer-join on the key and look at the mismatches. Zero rows
  both ways is the only acceptable result.
- Look at the rows, not just the aggregate. Aggregates hide almost everything.
- Test the edge cases deliberately: the entity with no children, the duplicate,
  the NULL, the row exactly on the date boundary.

## Habits I want kept

- Explicit column lists. No `SELECT *` in anything that lands somewhere.
- Qualify every column with its table alias once there's more than one table.
- Aliases that mean something. Not `a`, `b`, `c`.
- CTEs to give each step a name, one idea per CTE. A named step I can select
  from is worth more than a clever single statement.
- Comment the *why* — why this filter exists, why this join is safe. The SQL
  already says what.
- Dates: be explicit about inclusive/exclusive bounds. I prefer
  `>= start AND < end` over `BETWEEN`, so the boundary is never ambiguous.
- Say which timezone a timestamp is in, or convert it where it enters.

## Dedup

There's usually no such thing as a duplicate — there's a row that's duplicated
under *some* definition of key. Say the key out loud first.

`ROW_NUMBER() OVER (PARTITION BY key ORDER BY <tiebreaker>)` then filter to 1 is
my default, and the tiebreaker has to be deterministic. If two rows tie on the
ordering column, the query is non-deterministic and will produce different
results on different runs — which is how I get "the number changed and nothing
changed".

`DISTINCT` as a fix for unexpected duplicates is a red flag: it hides the
fan-out instead of explaining it. Find the join first.

## Writes

- Prefer replacing a whole partition to appending, so a re-run is idempotent.
  See [data-pipelines.md](data-pipelines.md).
- `MERGE` needs a genuinely unique key on both sides. If the source has
  duplicates, most engines either error or pick arbitrarily; neither is what I
  want to discover in production.
- Transactions where the engine supports them, and check the affected-row count
  against the SELECT before committing.

## Performance, in the order I try things

1. Am I scanning more partitions than I need? Filter on the partition column,
   and make sure the filter is a form the engine can actually prune on.
2. Am I moving more rows than I need? Filter and aggregate before joining, not
   after.
3. Is a join exploding and then being collapsed by a DISTINCT or GROUP BY?
4. Read the plan before guessing. Then change one thing at a time.

Slow is a problem worth solving after correct. I don't tune a query I haven't
verified.

## For an assistant

- Give me the SELECT first, always, with the same WHERE the write would use.
- Tell me the grain of what you wrote and which joins you expect to be
  many-to-one, so I know what to check.
- If you don't know my engine's dialect, ask rather than emitting something
  plausible-looking. Warehouse dialects differ most in exactly the places that
  matter: dates, window frames, MERGE, and qualify/semi-join syntax.
