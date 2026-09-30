---
name: plan-ticket
description: Turn a feature request or ticket into a reviewed implementation plan saved to docs/plans/, with ADRs for significant decisions. Use when the user asks to plan a ticket, feature or larger change before implementing it.
argument-hint: <ticket text | GitHub issue number or URL | path to a ticket file>
---
Plan the ticket in `$ARGUMENTS`. Do not edit any code in this skill; the only files you write are the plan,
ADRs and `.git/info/exclude`. Implementation starts only after the user approves the plan.

# 1. Get the ticket
- Issue number or GitHub URL: `gh issue view <ref> --comments`.
- File path: read it.
- Otherwise treat the arguments as the ticket text. If there are none, ask for the ticket.
Derive `<ticket-id>` (issue number or tracker key, if any) and a short kebab-case `<slug>` from the title.

# 2. Understand the codebase
- Read `.claude/CLAUDE.md` and the `.claude/rules/` files that apply to the areas the ticket touches.
- Find the existing feature most similar to this one and trace it end to end (transport, service,
  repository, migrations, tests). The plan should reuse its structure unless there's a reason not to.
- Note every file, type and function the change will touch, with paths.

# 3. Resolve open questions
Before designing, ask the user (AskUserQuestion) the questions whose answers change the design: ambiguous
requirements, API shape, data ownership, compatibility constraints. Skip questions you can answer from the
code or a sensible default; state those assumptions in the plan instead.

# 4. Write the plan
Write `docs/plans/<ticket-id>-<slug>.md` (just `<slug>.md` without an id). Keep every section; write "None."
where one doesn't apply so it's visible that it was considered.

```markdown
# <ticket-id>: <title>
Status: draft
Source: <issue link, file path, or "pasted">

## Goal
<one paragraph: the problem and the outcome>

## Acceptance criteria
- <testable statements>

## Assumptions
- <defaults chosen without asking>

## Context
<existing code this builds on, with paths; the analogous feature being followed>

## Design
<how it works: components, data flow, error handling>

### Decisions
<only real choices. For each: options, pros and cons, choice, reason. Link the ADR if one was written.>

## Changes
| File | Action | What |
|------|--------|------|

## API and proto changes
<new or changed endpoints, RPCs, messages, fields; wire and backward compatibility>

## Data and migrations
<schema changes, backfill, locking on large tables, deploy ordering between schema and code, rollback>

## Implementation order
1. <step that builds and tests on its own>

## Test plan
<unit, integration and manual checks, mapped to the acceptance criteria>

## Operations
<rollback, logs and metrics, config and flags>

## Risks and open questions

## Out of scope
```

# 5. ADRs
Write an ADR only for a decision that is hard to reverse or affects more than this ticket: new dependency or
service, storage choice, schema shape, public API or proto contract, service boundary. Use the next free number
in `docs/adr/` (`0001` if empty):

```markdown
# NNNN: <decision>
Date: <YYYY-MM-DD>
Status: accepted
Related: docs/plans/<plan file>

## Context
## Decision
## Consequences
<positive and negative>
## Alternatives considered
<each with why it lost>
```

# 6. Keep plans and ADRs out of git
For each of `docs/plans/` and `docs/adr/`, if `git check-ignore -q <dir>` fails, append the directory to
`.git/info/exclude`.

# 7. Review
Run the `plan-reviewer` agent with the plan path and the original ticket text. Revise the plan for each finding
you agree with; for any you reject, tell the user why. Set `Status: reviewed`.

# 8. Hand off
Give the user the plan path, the ADRs written, the assumptions made, and the review findings you rejected.
Then stop and wait. When the user approves, set `Status: approved` and implement in the plan's order.
