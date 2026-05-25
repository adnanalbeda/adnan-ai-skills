---
name: grill-agents-with-docs
description: Runs an unattended grilling session against project docs where the main agent interviews, delegates option research to subagents, chooses answers via evidence comparison, and updates CONTEXT.md/ADRs inline. Use when the user wants grill-with-docs behavior but will be AFK or wants agents to select answers automatically.
---

<what-to-do>

Interview the plan relentlessly about every aspect until you reach a coherent, evidence-backed understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. The main agent remains the interviewer throughout the session.

This is unattended mode: do not wait for the user to choose answers. For each question, generate concrete answer options as usual, delegate option research to subagents, delegate final comparison to a separate subagent, then continue using the selected answer as if the user chose it.

For every question, suggest at least two concrete answer options unless only one option is genuinely viable. Mark exactly one as recommended after the agent comparison is complete, put it first unless repo evidence makes another ordering clearer, and use this format:

````md
---------- option a ----------

1. option-a (recommended)
description: <what this chooses>
example:
```
<small concrete example or demo>
```
reason: <why this is recommended based on evidence>

---------- option b ----------

2. option-b
description: <what this chooses>
example:
```
<small concrete example or demo>
```
reason: <why someone might choose this instead>
````

Then write the selected answer in one short sentence and proceed to the next question.

If a question can be answered by exploring the codebase, explore the codebase before creating answer options.

Before starting grilling session of an aspect, write brief summary about it, and estimated number of questions:

````
current aspect: ab
brief: abc
estimated no. of questions: 00
````

When aspect is over and moving into new aspect, write brief summary of previous aspect, estimated number of questions and real number of questions, and a summary about the current, also estimated number of questions.

````
previous aspect: ab
brief: abc
estimated no. of questions: 00
real no. of questions: 00
summary:
abcd

---

current aspect: ab
brief: abc
estimated no. of questions: 00
````

</what-to-do>

<unattended-agent-loop>

## Per-question loop

1. State the question and draft answer options.
2. If there are multiple answer options, launch one research subagent per option in parallel.
3. Each option subagent must argue for its assigned option using project evidence, docs, code references, constraints, trade-offs, and risks. It must not compare options except where direct evidence weakens its assigned option.
4. Launch a separate comparison subagent after option analyses return. The comparison subagent must choose the best answer based only on evidence and session goals, then return:
   - selected option
   - strongest evidence
   - rejected options and why
   - confidence level
   - unresolved assumptions
5. The main interviewer must sanity-check the comparison. If evidence is weak, conflicting, or missing, do more code/docs research or launch a targeted follow-up subagent before accepting.
6. Reorder the options so the selected option is first and marked `(recommended)`.
7. Record the selected answer in the decision tree, update `CONTEXT.md` or ADRs when needed, and continue to the next question without asking the user.

## Subagent prompt templates

Option research subagent:

```md
Research this answer option for an unattended grilling session.

Question: <question>
Assigned option: <option name and description>
Known context: <brief session context and relevant prior answers>

Argue for this option using repo evidence, docs, code references, constraints, trade-offs, and risks. Be honest about weaknesses. Do not choose the final answer. Return concise bullets with file paths and line references when available.
```

Comparison subagent:

```md
Compare option analyses for an unattended grilling session.

Question: <question>
Session goals: <goals and constraints>
Prior answers: <relevant prior decisions>
Option analyses: <summaries or full results>

Choose the best answer based on evidence, coherence with prior answers, reversibility, risk, and fit with project docs/code. Return selected option, strongest evidence, rejected options and why, confidence, and unresolved assumptions.
```

## Selection rules

- Prefer the option most consistent with existing domain language and documented decisions.
- Prefer options that reduce ambiguity and preserve future reversibility when evidence is tied.
- Do not pick the most complex option unless evidence shows simpler options fail.
- If all options are bad, synthesize a better option and run one focused research pass for it before selecting.
- If the choice depends on a hard external fact that cannot be inferred from the repo, record the assumption explicitly instead of blocking.

</unattended-agent-loop>

<supporting-info>

## Domain awareness

During codebase exploration, also look for existing documentation:

### File structure

Most repos have a single context:

```
/
+-- CONTEXT.md
+-- docs/
|   +-- grill-answers-tree/
|   |   +-- 0001-event-sourced-orders-answers.md
|   |   +-- 0002-postgres-for-write-model-answers.md
|   +-- adr/
|       +-- 0001-event-sourced-orders.md
|       +-- 0002-postgres-for-write-model.md
+-- src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has multiple contexts. The map points to where each one lives:

```
/
+-- CONTEXT-MAP.md
+-- docs/
+-- docs/
|   +-- grill-answers-tree/           <- system-wide grilling answers
|   +-- adr/                          <- system-wide decisions
+-- src/
|   +-- ordering/
|   |   +-- CONTEXT.md
|   |   +-- docs/grill-answers-tree   <- context-specific grilling answers
|   |   +-- docs/adr/                 <- context-specific decisions
|   +-- billing/
|       +-- CONTEXT.md
|       +-- docs/grill-answers-tree
|       +-- docs/adr/
```

Create files lazily - only when you have something to write. If no `CONTEXT.md` exists, create one when the first term is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user or prior answers use a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but this answer uses it as Y - resolve the conflict before continuing."

### Sharpen fuzzy language

When an option uses vague or overloaded terms, propose a precise canonical term. "This says 'account' - in this context that must mean Customer, not User."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force precision about the boundaries between concepts.

### Cross-reference with code

When an option states how something works, check whether the code agrees. If you find a contradiction, surface it and use it in option comparison.

### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch these up - capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

`CONTEXT.md` should be totally devoid of implementation details. Do not treat `CONTEXT.md` as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** - the cost of changing your mind later is meaningful
2. **Surprising without context** - a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** - there were genuine alternatives and one was picked for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

</supporting-info>
