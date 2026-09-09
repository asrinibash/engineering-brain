<!-- TEMPLATE. Copy to the project root as AGENTS.md. Replace <BRAIN> with the
     absolute path to this repo, e.g. C:\Users\<me>\engineering-brain — then
     delete this comment. -->

# Agent instructions

The person you are working with keeps a knowledge base at `<BRAIN>`. It covers
how they debug, what they want escalated, and the shell and stack they use.
Read it before writing code, running commands, or answering technical questions.

## Read order

Start with `<BRAIN>/INDEX.md`, which lists the files under `<BRAIN>/knowledge/`
and the order to read them in. Read the files themselves; do not work from a
summary, and do not copy their contents into this file.

The highest-value file is `<BRAIN>/knowledge/20-method.md` — the debugging
method and the rules about mutating data. If you read only one, read that.

## Before acting

Say briefly what you took from it, especially the debugging method and the
stop-and-ask rules. Then work.

## Capturing lessons

If the human says "add that to the brain": append one line to `<BRAIN>/INBOX.md`,
dated `YYYY-MM-DD`, generalised so it holds outside this project — no table or
DAG names, hosts, tickets, or people. Append only. Do not edit
`<BRAIN>/knowledge/` from here; that happens in a separate review pass.

## Scope of this file

This file is a pointer. Instructions about *this project* can be added below.
Instructions about how the human works belong in `<BRAIN>/knowledge/`.
