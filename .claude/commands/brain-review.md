---
description: Promote inbox items into knowledge/, one reviewed diff
---

Run the promotion pass on my engineering brain. Read `INDEX.md` and `CLAUDE.md`
in this repo first — the rules there govern this whole task.

## What to do

1. **Read `INBOX.md`.** If it's empty apart from the header and comment, say so
   and stop.
2. **Read every file in `knowledge/`** before proposing anything. Most inbox
   items are a sharper version of something already written down, and the right
   move is usually to edit an existing sentence rather than add a new one.
3. **For each inbox item, decide one of:**
   - **Promote** — it's transferable, and it's happened in more than one project
     or is clearly general. Say which file and which section.
   - **Hold** — plausible but only seen once. Leave it in the inbox and say why.
   - **Drop** — project-specific, already covered, or no longer true. Say which.
4. **Check the boundary.** Anything naming a table, DAG, schema, host, ticket,
   dashboard or person doesn't go in. Rewrite it to the general form or hold it.
5. **Propose the diff.** Show me the changes before making them, grouped by
   file, with the inbox line each one came from. Then wait for me.

## Rules for the edits themselves

- Prefer editing an existing section over adding a new one. This repo only works
  while it's short enough to actually get read.
- One copy of everything. If two files would say it, put it in the right one and
  link from the other.
- Match the existing voice. First person, direct, specific. Don't smooth it into
  documentation prose — that's the failure mode I care most about avoiding.
- Don't restructure files I didn't ask about. No reordering sections, no
  renaming headings, no reformatting untouched prose.

## Finishing

- Remove the promoted lines from `INBOX.md`. Leave held items where they are.
- Put it on a branch and open a PR — never commit to main.
- Tell me what you held and what you dropped, and why. That list is the part I
  most want to see, because it's where the judgment happened.
