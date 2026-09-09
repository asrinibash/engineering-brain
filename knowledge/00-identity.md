# Identity

## Who I am

I'm a data engineer. Most of my work is config-driven Airflow pipelines — the
kind where the DAG file is thin and the actual behaviour lives in config that
gets validated and expanded into tasks. Around that: a lot of SQL, Python and
pandas for the parts that don't fit in SQL, and a fair amount of time reading
other people's pipelines to work out why a number moved.

I'd rather be right than fast. A pipeline that fails loudly is a nuisance; a
pipeline that quietly writes wrong numbers is a real problem, and most of my
judgment calls trace back to that difference.

## What I actually spend time on

- Adding to and changing config-driven pipelines — usually a config change plus
  a small amount of code, not greenfield DAGs
- Debugging runs that failed, or worse, runs that succeeded with wrong output
- Writing and tuning SQL, and checking that a query means what I think it means
- Data correction: backfills, restatements, one-off repairs to landed data
- Reviewing pipeline changes before they merge

## What I'm accountable for

- **The numbers downstream.** If a report is wrong, it's mine to explain and
  mine to fix, regardless of which layer broke.
- **Not breaking production.** Changes go through review; I don't get to be the
  person who pushed a fix straight to main at 6pm.
- **Being able to show my work.** If I claim a number is right, I should be able
  to produce the query and the output that says so.

## What I'm not

- **Not the business owner of the data.** I don't get to decide what "active
  customer" or "revenue" means. When a fix depends on a definition, that's a
  question for the stakeholder, not something for me or you to pick.
- **Not a DBA or infra engineer.** I work within the platform I'm given. If the
  answer is "change the cluster config", I'm escalating, not doing it.
- **Not an app or frontend developer.** Assume I'm rusty on anything that isn't
  data, and say so plainly rather than assuming I'll follow.

## How I like to learn things

Show me the mechanism, not the incantation. If you tell me to add a parameter,
I want one line on what it actually changes — otherwise I'm carrying a magic
string around forever. I'm fine with being told I'm wrong; I'm not fine with
being told I'm wrong without the evidence attached.

See also: [10-collaboration.md](10-collaboration.md) for how I want that
delivered, and [20-method.md](20-method.md) for how I work through a problem.
