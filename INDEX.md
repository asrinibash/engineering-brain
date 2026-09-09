# INDEX

This repo is my portable engineering brain. If you're an AI assistant working
with me on anything technical, read `knowledge/` before you write code, run a
command, or answer a question about my work.

## Read order

1. `knowledge/00-identity.md` — who I am, what I build, what I'm accountable for
2. `knowledge/10-collaboration.md` — how I want you to work with me
3. `knowledge/20-method.md` — how I debug and how I change things
4. `knowledge/30-environment.md` — my machine, shell, and stack
5. `knowledge/patterns/*.md` — craft notes for SQL, pandas, and pipelines. Read
   the one that matches the task in front of us; skip the rest.

If you only have room for one file, read `20-method.md`. Everything else is
context; that one is the part I'll notice you ignoring.

## Rules for this repo

- **One copy of everything.** Content lives in `knowledge/`. The files in
  `adapters/` (`CLAUDE.md`, `.cursorrules`, `AGENTS.md`) are pointers, never
  copies. If you catch yourself pasting a paragraph into an adapter, stop and
  point at the source instead.
- **Nothing company-specific.** No schemas, table names, DAG names, hostnames,
  IPs, dashboard links, ticket IDs, colleague names or emails, and no business
  rules that only make sense at one employer. Those live in that company's repo.
  The test: would this still be true at my next job? If not, it doesn't go here.
- **Plain markdown.** No frontmatter, no build step, no tool-specific syntax.
  This has to stay readable by a model I haven't met yet.
- **Projects are an index, not a home.** `projects/README.md` lists what I'm
  working on and links out. Project detail doesn't live in this repo.

## When I bootstrap you with this

Read the files above, then tell me in three or four lines what you took away —
especially my debugging method and the things I want you to stop and ask about.
I'd rather catch a misread in a summary than in a diff.

## Keeping this current

When I say "add that to the brain" from another repo, append one dated,
generalised line to `INBOX.md` — don't edit `knowledge/` from outside. Items
move from the inbox into `knowledge/` in a separate review pass, where they get
deduped against what's already written. Prefer editing an existing section over
bolting on a new one; this only stays useful if it stays short. If something in here contradicts what
I've told you in chat, chat wins for today's task — and tell me the file is
stale so I can fix it.
