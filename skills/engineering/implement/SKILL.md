---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If the user passes a ticket reference, fetch it from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

## Check the ticket

Unless the user says the ticket came from `/to-tickets`, check it before writing code: a hand-written ticket drifts, its body going stale and its domain claims parting from the docs.

1. Read the body on its own, then the comments (on GitHub, `gh issue view <n> --json body`). The body is the spec; a comment is history unless it says it amends the body.
2. Take blockers from the tracker's native links and labels, not from the body's text.
3. Check every claim the body makes (domain terms, rules, figures, file and symbol names) against the glossary, the ADRs it touches, and the code. Grep for the sections you need.
4. If the ticket removes an output (a file, a column, a plot), it names who updates the docs that cite it.

Done when every claim is confirmed or listed as a conflict. With none, carry on. With any, stop: give the user the list and recommend `/grill-with-docs` on the ticket first. If the ticket will touch more files than one session can hold, recommend `/to-tickets` or `/implement-spec` instead.

## Build

Call the Skill tool with "tdd" where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, call the Skill tool with "code-review" to review the work.

## Sweep the deferrals

When the work is a ticket, other open issues may hand work to it ("#6 adds the check"). Search the tracker for every open issue that cites the ticket, across every repo it spans; the repo's issue-tracker doc names the search tool if it has one. For each issue that defers work to this ticket, either do that work in this run or fix the issue's body to say where the work now goes.

Done when every citing issue is read and none still points at this ticket for work it did not do.

## Commit

Commit your work to the current branch.
