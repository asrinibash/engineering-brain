# Patterns: Python and pandas

Notes on the Python side of pipeline work. This is mostly about not being lied
to by a DataFrame.

## Inspection is the work

In a mirror notebook ([../20-method.md](../20-method.md)), after every cell that
produces or changes a frame I look at, in this order:

1. `df.shape` — did the row count change when I expected it to?
2. `df.dtypes` — is that column really a string? really a datetime?
3. `df.isna().sum()` — nulls are where the bugs are
4. `df.head()` and a targeted sample of the rows I care about, not just the top

Most pandas bugs are visible in one of those four and invisible in the output.

## dtype surprises I keep hitting

- An integer column with a single NaN becomes `float64`, and then IDs render as
  `1.0` and stop joining. Read IDs as strings when they're identifiers, not
  numbers I'll do arithmetic on.
- `object` dtype means "could be anything" — mixed types in one column will
  compare and sort in ways I won't predict.
- Datetimes: tz-aware and tz-naive won't compare, and mixing them raises or
  silently misaligns depending on the operation. Decide the timezone at the
  boundary where data enters and keep it consistent after that.
- `read_csv` guesses. When I care, I pass `dtype=str` and convert deliberately,
  so the conversion failures are mine to see rather than silently inferred.
- `pd.to_numeric(..., errors="coerce")` turns bad data into NaN quietly. Fine if
  I then count the NaNs and look at what got coerced; not fine as a reflex.

## Merges

`merge` is where row counts go wrong, so:

- Use `validate=` — `"one_to_one"`, `"many_to_one"`, `"one_to_many"`. It turns a
  silent fan-out into an exception, which is exactly the trade I want.
- Use `indicator=True` when I'm not sure the keys match, then look at the
  `_merge` counts. `left_only` rows are the thing I was about to lose.
- Check `len(df)` before and after every merge. Same discipline as SQL joins in
  [sql.md](sql.md).
- Key dtypes must match. `"123"` and `123` don't join and pandas won't warn.

## Assignment and copies

- `SettingWithCopyWarning` is not noise. It means my write may have landed on a
  temporary and disappeared. Resolve it, don't suppress it.
- Use `.loc[mask, col] = value`, not chained `df[mask][col] = value`.
- Be explicit with `.copy()` when I slice a frame and intend to modify it.
- I avoid `inplace=True`. It doesn't reliably save memory and it makes the
  notebook's cell-by-cell reruns non-idempotent — re-running a cell gives a
  different result than running it once, which breaks the whole mirror method.
- Related: write cells so re-running them is safe. Derive into a new name rather
  than mutating the same frame repeatedly down the notebook.

## groupby and aggregation

- `groupby` drops NaN keys by default. If NULL is a real category, pass
  `dropna=False` — otherwise rows vanish from the total and nothing tells me.
- `as_index=False` or `.reset_index()` so the result stays a plain frame.
- Prefer named aggregation (`.agg(total=("amt", "sum"))`) — it's readable and
  the output columns are predictable.
- `apply` on a groupby is slow and its return shape is hard to predict. Reach
  for a vectorised expression or a merge first; use `apply` when the alternative
  is genuinely unreadable, not as the first idea.

## Comparing outputs

When I've refactored something, I want a real comparison, not a glance:

- Sort both frames by key and reset the index before comparing — pandas compares
  by position, so unsorted frames disagree for no reason.
- `df.compare(other)` for a column-level diff, or merge on the key with
  `indicator=True` and inspect the non-`both` rows.
- Compare the aggregate *and* a sample of rows. Matching totals with different
  rows happens more often than it should.

## General Python habits

- Fail loudly. Bare `except:` and `except Exception: pass` hide the thing I
  needed to see. If I'm swallowing an error, log what I swallowed.
- No mutable default arguments.
- Type hints on function signatures that cross a module boundary; I don't need
  them on everything.
- f-strings, `pathlib` for paths (it survives Windows backslashes better than
  string concatenation — see [../30-environment.md](../30-environment.md)).
- Config and secrets come from config, never a literal in the module.
- Small pure functions where the data transformation lives, so I can call them
  from a notebook cell without dragging in the whole pipeline.

## For an assistant

- Don't hand me `.fillna(0)` or a `try/except` before we've established what's
  null and why. That's patching over the evidence.
- When you propose a transformation, tell me what it does to the row count.
- Prefer showing me an inspection cell alongside the change, so I can see the
  frame rather than trust the diff.
