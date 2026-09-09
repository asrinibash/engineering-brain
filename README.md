# engineering-brain

My portable engineering knowledge base. Private repo, plain markdown, no build
step.

The point: how I work shouldn't be trapped in one laptop, one employer, or one
AI tool's memory feature. Laptops get replaced, jobs change, and assistants
arrive with no idea how I debug or what I refuse to guess at. This repo is the
thing I hand them so they start from where I already am.

## What's in here

```
README.md            you are here — for humans
INDEX.md             for assistants — read order and the rules
CLAUDE.md            pointer for assistants working inside this repo
INBOX.md             raw capture, cleared out by the review pass
knowledge/
  00-identity.md     who I am, what I build, what I'm accountable for
  10-collaboration.md how I want an assistant to work with me
  20-method.md       how I debug and how I change things
  30-environment.md  my machine, shell, and stack
  patterns/
    sql.md
    python-pandas.md
    data-pipelines.md
projects/README.md   index of what I'm working on, links out
adapters/            thin pointer files for each tool
.claude/commands/    /brain-review — the promotion pass
```

`/brain` (capture) is installed globally at `~/.claude/commands/brain.md`, not in
this repo, so it works from any project.

## The two rules that keep it useful

**1. Content lives in exactly one place.** Everything real is under
`knowledge/`. Every tool gets a *pointer* to it, never a copy. The moment a
paragraph exists twice, one of them starts going stale and I stop trusting both.

**2. Nothing company-specific.** No schemas, table names, DAG names, hostnames,
IPs, dashboard links, ticket IDs, colleague names or emails, no business rules
that only make sense at one employer. The test: *would this still be true at my
next job?* If not, it belongs in that company's repo. This keeps the repo
portable and keeps me out of trouble when it moves with me.

Generalised is fine — "config-driven Airflow, so the first debugging question is
which config the run loaded" is transferable. "Check `dag_config/foo_v2.yaml`"
is not.

## Setting it up on a new machine

Clone it to a stable path — the adapters reference that path, so it's worth
keeping the same across machines.

```powershell
git clone <repo-url> $HOME\engineering-brain
```

## Wiring it into a tool

The pointer goes in whatever file that tool reads on startup. It only needs to
say "read this, first". Something like:

```
Before doing anything technical, read the files in
C:\Users\<me>\engineering-brain\knowledge\ — start with INDEX.md, which gives
the read order. Follow 20-method.md especially. Do not summarise it back into
this file; read the source.
```

Where that lives depends on the tool:

- **Claude Code** — the user-level `CLAUDE.md` in `~/.claude/`, so it applies in
  every project without touching any work repo.
- **Cursor** — `.cursorrules` in the project, or the global rules setting.
- **Anything that reads `AGENTS.md`** — project root.

`adapters/` holds the canonical text of each of these so I'm not rewriting the
pointer from scratch each time. Copy from there into position; don't grow them
into second copies of the knowledge.

**Careful with work repos.** A pointer file committed into a company repo leaks
a personal path into their history and puts my rules in front of my colleagues.
Prefer the user-level/global config, or a local ignored file. When in doubt,
global.

## Using it in a session

Start a new assistant with something like:

```
Read knowledge/INDEX.md and the files it lists, then tell me in a few lines what
you took away — especially my debugging method and what I want you to stop and
ask about, before you touch anything.
```

The summary-back step matters. It's much cheaper to catch "it didn't read
20-method.md" in three lines of prose than three files into a diff.

## Keeping it alive

- **Capture in the moment, promote later.** Mid-task, say "add that to the
  brain" (or run `/brain`) and one dated line gets appended to `INBOX.md`. That
  is all that happens then — no edits to `knowledge/` from a work repo.
- **Run the review when the inbox reaches ~10 lines**, or monthly. In this repo,
  `/brain-review` reads the inbox, checks each item against what's already
  written, and proposes one PR. Promoted lines get deleted from the inbox.
- **The two-project rule.** Something graduates into `knowledge/` once it has
  come up in two different projects. Before that it's plausibly a quirk of one
  codebase, and it belongs in that project's own instructions file.
- Prefer editing an existing section to adding a new one. This is only useful
  while it stays short enough to actually be read.
- Review it when jobs change: that's the moment company specifics have a way of
  having crept in, and the moment the environment file goes stale.
- Everything here is a working draft. If a rule in `knowledge/` no longer
  matches how I actually work, the file is wrong, not me.

## Why plain markdown

No frontmatter, no schema, no generator. I want this readable by a tool that
doesn't exist yet, by a model I haven't used yet, and by me with nothing but a
text editor. Anything clever here is a liability.
