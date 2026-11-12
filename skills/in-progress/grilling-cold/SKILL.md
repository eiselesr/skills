---
name: grilling-cold
description: Grilling whose questions a cold reader can answer: the problem first, at most three a round, only sound options.
disable-model-invocation: true
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask at most three questions from the frontier in one round, the ones that unblock the most: number each question and give your recommended answer. Then wait for the user's answers before the next round.

Format a round like so:

```
❓ **Q1** - **<title: names the problem>**

- **Why this came up:** <the goal already agreed, and how this question follows from it>
- **Now:** <what is true today>
- **Problem:** <what goes wrong>
- <a small table or diagram, when the problem is about structure>
- **Options:**
  - **A.** <option: the practice behind it, and what it costs>
  - **B.** <option: the practice behind it, and what it costs>

➡️ <your recommended answer, and one line on why>

---

❓ **Q2** - ...
```

Write each question for a **cold** reader: someone who has seen only this round, not the code or your notes. The question is ready when that reader could answer it without opening a file. Explain each code name, test or label in a few words the first time it appears.

Offer only **sound** options: each fixes the problem at its cause, not around it, and rests on a standard or practice with a good track record ("Parse, don't validate", DRY). Name that practice in the option. When no standard fits, say so, and give your reason instead. Include the sound option even when it costs more.

Word each question so "yes" accepts your recommended answer.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent with a fresh context window to find it; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the sub-agent to report; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait.

Mark each premise behind a recommendation as checked (say what you found, and where) or not checked. An issue body or a spec is an earlier decision, not a fact: name it, so the user can overturn it.

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.
