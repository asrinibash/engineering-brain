# Method

How I work through a problem. If you follow one file in this repo, follow this
one. The order matters more than any individual step.

---

## 1. The notebook mirror — how I debug

When something is broken, I do **not** start editing the file. I mirror it.

1. **Copy the broken code into a notebook, verbatim.** Not a simplified version,
   not "the relevant part", not a rewrite from memory. The same lines, split
   into cells at natural boundaries. If it doesn't reproduce, the difference
   between the mirror and the original is itself the bug.
2. **Block every side effect first, before running anything.** Writes become
   no-ops or writes to a scratch location. Sends, triggers, emails, API calls
   get stubbed. Anything that mutates shared state is disabled at the top of the
   notebook and I can see the stub. The notebook is read-only until I say
   otherwise.
3. **Run it section by section.** Not the whole thing. Cell by cell, top to
   bottom, looking at the actual object after each one — shape, dtypes, null
   counts, a sample of rows, the real value of the config that got loaded.
4. **Observe every failure before fixing anything.** When a cell fails, I don't
   fix it and move on. I note it and keep going far enough to find out whether
   there are three failures or one failure wearing a disguise. Fixing the first
   error you see is how you spend a day on the wrong bug.
5. **Only then form a hypothesis**, in one sentence, phrased so it could be
   wrong. Then test it in the notebook.
6. **Fix progressively in the notebook.** Change one thing, re-run from the
   cell that matters, confirm the object looks right. Repeat. The notebook
   becomes the record of what was actually wrong.
7. **Edit the real file last**, once, applying only the changes the notebook
   proved. Then re-run against the real thing.

### What this means for you

- Don't jump to a patch. If I've described a failure and you reply with a diff,
  you've skipped the whole method.
- Help me build the mirror: identify the entry point, what config it loads, what
  the side effects are and where they need stubbing.
- When I paste cell output, read it. The shape and the nulls are the evidence.
- Suggesting "just add a try/except" or "just add `.fillna()`" before we know
  what's null and why is the opposite of this.
- If you genuinely think the mirror isn't worth it — a one-line typo, an obvious
  import error — say so and say why, and I'll usually agree. Don't just skip it
  silently.

---

## 2. SELECT before UPDATE

No statement that changes data gets written until I've seen the rows it will
touch.

- Write the `SELECT` with the exact same `WHERE` clause first. Look at the count.
  Look at a sample. Confirm the count is the number I expected — and if I can't
  say what number I expected beforehand, I'm not ready to run it.
- Then convert to `UPDATE` / `DELETE` / `MERGE` **without touching the WHERE**.
- Wrap it in a transaction where the engine supports it. Check the affected-row
  count against the SELECT count before committing.
- Know how to undo it before running it. If the answer is "restore from backup",
  that's an escalation, not a plan.
- Same rule for pandas: look at `df[mask]` — count and sample — before assigning
  through the mask.
- Same rule for files and state: look at what's there before overwriting.

Applies to backfills too. A backfill is an UPDATE with better PR.

---

## 3. Evidence discipline

- A number without the query that produced it isn't a number, it's a rumour.
- When two sources disagree, find out which one is wrong before choosing. "Close
  enough" on a reconciliation means something is broken and hasn't surfaced yet.
- Reproduce before fixing. If I can't make it fail on demand, I don't know what
  I've fixed.
- After a fix, verify the *original symptom* is gone — not just that the error
  message changed.

---

## 4. Escalate, don't guess

Business decisions go to the person who owns the business. If a fix requires
knowing what "active", "billable", "in scope" or "duplicate" means, that's a
question, not an inference — even when the data seems to suggest an answer.

Write the escalation as: what's ambiguous, the options, what each one would do to
the numbers, and what I'd recommend. Then wait. Do not pick the option that
makes the pipeline green.

Also escalate: anything needing infra/platform changes, anything with a legal or
privacy dimension, and anything where the honest answer is "the data has been
wrong for a while and people have been using it".

---

## 5. Change discipline

- **Never push to main.** Branch, PR, review, merge. No exceptions, including
  for one-line fixes and including when I'm in a hurry.
- Commit in small, self-describing units. The message says why, not what — the
  diff already says what.
- Don't mix a fix and a refactor in one PR.
- Config-driven means the config change *is* the change. Review it as carefully
  as code, and say explicitly what it changes about the produced tasks.
- Before opening the PR: what did I verify, and what am I asking the reviewer to
  take on trust? Put that in the description.
- Don't `git add -A`. Stage deliberately.
- Never rewrite shared history, never force-push a branch someone else may have.

---

## 6. When I'm stuck

Say what I know, what I assumed, and where the assumption stopped being checked.
Nine times out of ten the bug is in the gap between those. Good things to ask me
at that point: "what's the last cell where the object looked right?" and "which
part of this have you actually run versus read?"
