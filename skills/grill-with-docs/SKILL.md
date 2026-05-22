---
name: grill-with-docs
description: Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates documentation (CONTEXT.md, ADRs) inline as decisions crystallise. Use when user wants to stress-test a plan against their project's language and documented decisions.
---

<what-to-do>

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer. For each answer, explain the decision effect and provide example or demo.

For every question, suggest at least two concrete answer options. Mark exactly one as recommended, put it first unless repo evidence makes another ordering clearer, and use this format:

````md
1. option-a (recommended)
description: <what this chooses>
example:
```
<small concrete example or demo>
```
reason: <why this is recommended>

2. option-b
description: <what this chooses>
example:
```
<small concrete example or demo>
```
reason: <why someone might choose this instead>
````

Ask the questions one at a time, waiting for feedback on each question before continuing.

When called by a strict hands-off parent workflow such as `oh-my-ai-skill`, do not wait for routine user feedback. Answer from repo/docs/code first. If still unresolved, propose at least two concrete options, then run independent resolution agents before choosing:

1. Spawn one agent per option to study that option's consequences, evidence, risks, and downstream effects. Example: `agent-1: option-a`, `agent-2: option-b`.
2. Collect each option agent's summary with evidence and recommendation.
3. Spawn a resolver agent to compare the option summaries and choose the best decision based on evidence, reversibility, risk, and fit with existing docs/code.
4. Record the resolved decision, evidence, rejected option, and any remaining blocker in `docs/grill-answers-tree/` or the parent run notes.

Only stop for the user if the resolver finds no safe local decision because the unresolved choice materially changes behavior, data shape, external contracts, security, cost, or irreversible scope.

If a question can be answered by exploring the codebase, explore the codebase instead.

</what-to-do>

<supporting-info>

## Domain awareness

During codebase exploration, also look for existing documentation:

### File structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
│   ├── grill-answers-tree/
│   │   ├── 0001-event-sourced-orders-answers.md
│   │   └── 0002-postgres-for-write-model-answers.md
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has multiple contexts. The map points to where each one lives:

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

Create files lazily — only when you have something to write. If no `CONTEXT.md` exists, create one when the first term is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch these up — capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

`CONTEXT.md` should be totally devoid of implementation details. Do not treat `CONTEXT.md` as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

### Track the decisions tree.

For each grilling session, create a file appropriate for the topic of discussion under `docs/grill-answers-tree/`.
After each concrete answer, write the question and the answer in it.

If the topic of discussion has two or more aspects that need discussion, define these aspects, mention the current one as open and others as deferred until in scope of discussion. Use the format in [DECISION-TREE-FORMAT.md](./DECISION-TREE-FORMAT.md).

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

</supporting-info>
