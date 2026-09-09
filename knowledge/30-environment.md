# Environment

My machine and stack. The point of this file is that you stop suggesting
commands that don't run here.

## Shell

**Windows. PowerShell is my default shell. Git Bash is installed and I'll use it
when a POSIX script genuinely needs it.**

When you give me a command, pick one shell and say which. Don't hand me
something that's half PowerShell and half bash.

PowerShell things to get right:

- **No bash heredocs.** `<<'EOF'` is a parse error. For multi-line strings use a
  single-quoted here-string, with the closing `'@` at column 0:

```powershell
git commit -m @'
line one
line two
'@
```

- No `&&` or `||` chaining in Windows PowerShell 5.1. Use `;` for sequential,
  or `A; if ($?) { B }` for conditional.
- No ternary, no `??`, no `?.`.
- `$env:NAME` to read and set env vars, not `export` and not `%NAME%`.
- Redirect to `$null`, not `/dev/null`.
- No `head`, `tail`, `which`, `touch`, `wc -l`, `mkdir -p` — use the cmdlets.
- Quote paths with spaces; call exes with spaces via `& "C:\path\app.exe"`.
- Paths are backslashes and drive letters. Watch for them turning into escapes
  when they end up inside Python strings — use raw strings or forward slashes.

If a script is genuinely easier in bash, say "run this in Git Bash" and I will.
Just be explicit about it.

## Python

- Local work is Python in a virtualenv/conda env; assume it may not match the
  runtime version in the pipeline environment, and say so when version matters.
- pandas for the in-Python data work. See
  [patterns/python-pandas.md](patterns/python-pandas.md).
- Jupyter notebooks are a first-class debugging tool for me, not a toy — see the
  mirror method in [20-method.md](20-method.md). Notebooks are scratch space:
  they don't get committed to production repos unless I say otherwise.

## Pipelines

- **Airflow, config-driven.** The DAG file is thin; behaviour comes from config
  that gets validated and expanded into tasks. So:
  - When I describe a bug, the first question is usually "which config did this
    run actually load", not "what does the DAG do".
  - A change to config is a change to production. Review it like code.
  - Show me the resolved/expanded config, not just the source config, when
    they're not obviously the same thing.
- Task-level state (clearing, re-running, marking success) is production
  mutation. Ask first — see [10-collaboration.md](10-collaboration.md).
- Scheduled/interval semantics bite regularly. When dates are involved, state
  explicitly which date you mean: logical/data date vs wall-clock run time.

## SQL

Warehouse SQL is a large part of the job. Dialect specifics belong in
[patterns/sql.md](patterns/sql.md), not here. The non-negotiable is SELECT
before UPDATE, in [20-method.md](20-method.md).

## Git

- Git on Windows: watch line endings and case-only renames.
- Never push to main; PR review required.
- No interactive rebase / `add -i` in tooling — it won't work in these harnesses.

## Editors and assistants

I move between tools deliberately, which is why this repo exists. Each tool gets
a thin pointer file in `adapters/` that says "read `knowledge/`". If you're
being asked to set up a new tool, add a pointer — never a copy.
