# Phase boundaries

A **phase** is a chunk of work inside a session: the grilling, the implementation, the QA. The definition is fuzzy on purpose: a phase ends when you think *"ok, we're done with that"*.

The **phase boundary** is the gap between two phases, and it is the only place this decision belongs. Mid-phase there is no decision to make: continue, or split the work that's left into subagents. Compacting mid-phase makes the agent lose the thread. A tangent that comes up mid-phase is a **side question**, with its own tools: see [Side questions](#side-questions).

## The five options

| Option       | What it does                                                    |
| ------------ | --------------------------------------------------------------- |
| **Continue** | Stay in the session. No context switch at all.                    |
| **`/clear`** | Empty the context window and start from nothing.                  |
| **`/handoff`** | Write a portable markdown file and seed a session anywhere with it. |
| **Subagent** | Send the task to its own context window and get a report back.     |
| **`/compact`** | Compress this context and seed a fresh session with the summary.  |

## The tree

Work top to bottom at the boundary. The first **yes** wins.

**1. Can you continue in this session?** Two things make the answer yes: the next phase needs this phase as a **primary source**, or you have enough [smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone) left (~150k tokens) for the next phase to fit. Grilling → implementation is the standard yes: the implementation wants the reasoning verbatim, not a summary of it. Continue costs nothing and loses nothing, so rule it out before anything else.

**2. Is the context irrelevant to what comes next?** Is everything in this session (the exploration, the decisions, the dead ends) disposable? If so, **`/clear`**. It is the cheapest move on the board: it takes no time and hands back the whole window. `/clear` also isn't terminal: the old session stays resumable.

The cost of getting this wrong is one-way. Clear a *relevant* context and you lose the **why** behind what you built, and no amount of reading the diff back gets it returned.

Before a `/clear`, ask the session what a fresh session would need that isn't already in files, issues or commits, and write it there: decisions to the issue or an ADR, steps to the plan, terms to `GLOSSARY.md`. If the answer is a lot of reasoning, the context is relevant after all: go on down the tree.

**3. Do you need to hand off?** `/handoff` is narrow. You need it only when you are:

- swapping to a **new harness** (Claude → Codex),
- moving to a **new directory** or repo,
- or sending the work to a **colleague**.

That list is the whole clause. What `/handoff` buys is **portability**: a file that travels. If nothing is travelling, you don't need it.

**4. Can the task be done AFK?** Is it scoped tightly enough to run with you away from the keyboard, no steering? Then send it to a **subagent** and leave this session untouched. Automated review is the standard case: the agent reads the diff and reports, and you aren't needed while it does.

**5. Otherwise, `/compact`.** Relevant context, same harness, same directory, and you need to stay in the loop: this is where the tree lands, and it lands here often. Pass it an instruction (`/compact we're going to QA this area`) so the summary keeps what the next phase needs.

`/compact` is the **default, not the first reach**. It sits at the bottom because the four questions above it are all cheaper or more precise. The failure mode when people start here is a fresh session that is confidently wrong about a decision the summary flattened.

## Side questions

A **side question** is a tangent that comes up mid-phase: something in the session to explore, where the answer is usually small. The goal is to explore it without growing the main session, then carry the small answer back by pasting it or writing it to a file. Pick by what the tangent needs:

| The tangent                                          | Tool               | Why                                                                                                    |
| ---------------------------------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------ |
| One question about what's already in context         | **`/btw`**         | Sees the full context; no tools; the answer stays out of history. One question, not a conversation.     |
| Exploration you steer, with tools, needing this context | **`/branch <name>`** | Switches you into a copy of the session and keeps the original; `/resume` returns. Full tools, edits in place. |
| A task you needn't steer, needing this context        | **`/subtask <task>`** | A forked subagent in the background; its result returns to this session.                              |
| Needs nothing from this session                       | **New session** or **subagent** | Starts small instead of copying this session.                                               |

A branch or subtask starts as big as the session it copies. When the session is already big, `/compact <the tangent>` inside the branch first.

When the tangent turns out to be a chunk of work, open an issue for it and finish the phase: the issue is a lasting handoff, worked later in a fresh session. When it must happen now, pick from the table, or `/handoff` to a new session when this session is big or the work lives in another repo.

Already explored the tangent in the main session? `/rewind` to the prompt where it started and pick **Summarize from here**: a compact of the tangent alone, with the originals kept in the transcript.

## Primary and secondary sources

Every move except **Continue** turns a **primary source** into a **secondary source**: the session as it happened, replaced by a summary of it. The trade is always the same shape:

| Source                            | Information | Noise | Room to move |
| --------------------------------- | ----------- | ----- | ------------ |
| Primary (Continue)                | Full        | Lots  | Little       |
| Secondary (`/compact`, `/handoff`) | Lossy       | Less  | Lots         |

This is why question 1 comes first. You only pay the lossiness when staying costs more than it saves.

## These are judgement calls

The questions are not objective: each has taste in it, and the same boundary can go two ways on two days. The value is in asking them **in order**, at the boundary rather than in the middle of the work.
