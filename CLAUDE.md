# CLAUDE.md

This repo is my engineering knowledge base. Read [INDEX.md](INDEX.md) first — it
gives the read order for `knowledge/` and the rules this repo runs on.

Two things are usually happening in here, and they want different behaviour.

## If we're doing engineering work

Read `knowledge/` and follow it. `knowledge/20-method.md` is the one that
matters most.

## If we're editing this repo

That's the more common case, and the rules are:

- **One copy of everything.** Content lives in `knowledge/`. `adapters/` holds
  pointer templates. If a paragraph is about to exist in two files, it goes in
  `knowledge/` and the other file links to it.
- **Nothing company-specific.** No schemas, table or DAG names, hostnames, IPs,
  dashboard links, ticket IDs, colleague names or emails. The test: would this
  still be true at my next job? `projects/README.md` is where this is hardest —
  read the warning at the top of it before touching that file.
- **Propose a diff, don't rewrite.** Suggest the change against the specific
  file and section; don't restructure files I didn't ask about, and don't
  reformat prose to sound more like documentation. The voice is deliberate.
- **Prefer editing an existing section to adding a new one.** This only works
  while it's short enough to actually get read.
- **Plain markdown.** No frontmatter, no build step, no tool-specific syntax.

## When I say "add that to the brain"

We're already in the repo, so propose the diff directly: work out which file it
belongs in, generalise it so it isn't tied to one employer or one incident, and
show me the change. If it doesn't generalise, say so rather than writing a
vaguer version of a specific thing.

If it needs thought and I'm mid-task, put it in `INBOX.md` instead and say so —
that's the pile `/brain-review` works through later. From other repos the inbox
is the only route; direct edits to `knowledge/` happen here, with me watching.
