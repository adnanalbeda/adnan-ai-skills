---
name: lazy-docs
description: Lazy, choice-driven grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates documentation (CONTEXT.md, ADRs) inline as decisions crystallise. Use when user wants docs-aware planning pressure with lazy-grill interaction.
---

<what-to-do>

Use `lazy-grill` interaction style while doing docs-aware grilling: interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

This skill is the docs-aware variant of `lazy-grill`. Do not redirect to plain `lazy-grill` or `grill-with-docs`; apply the lazy-grill active-recommendation flow inside the docs-aware workflow.

Ask the questions one at a time, waiting for feedback on each question before continuing.

If the question/choice tool is available, use the `lazy-grill` active-recommendation flow instead of normal choice lists: list all candidate answers in the message, show the active recommendation with a small useful demonstration, then offer `Yes`, `Accept recommendation`, or `Choose this` for acceptance; dynamic `Show Option N` labels for option navigation; `Suggest Other Options` for agent-generated alternatives; and the freeform custom-answer input for user-supplied answers.

If a question can be answered by exploring the codebase, explore the codebase instead.

</what-to-do>

<lazy-choice-flow>

Before calling the question/choice tool, write a short setup that includes:

- The current question.
- All candidate answers with short tradeoffs.
- The active recommended answer.
- The smallest useful demonstration for the active recommendation, such as a tiny code sample, folder tree, state flow, architecture sketch, before/after behavior, domain example, glossary entry, or ADR snippet.

Then call the question/choice tool with acceptance and option-navigation choices for the active recommendation. Use dynamic option labels instead of generic forward/back labels so navigation cannot be mistaken for skipping to a later discussion point:

- First candidate: `Yes`, `Show Option 2`, and `Suggest Other Options`.
- Middle candidate: `Yes`, `Show Option <higher-number>`, `Show Option <lower-number>`, and `Suggest Other Options`.
- Last candidate: `Yes`, `Show Option <lower-number>`, and `Suggest Other Options`. Do not wrap to the first option. If the user rejects the last candidate, ask them to suggest other options.

If the design question itself is a yes/no decision, avoid confusing labels like "Yes to yes". Rename the acceptance action to `Accept recommendation` or `Choose this`, and phrase candidates as concrete outcomes such as `Create it` / `Skip it`, `Use X` / `Do not use X`, or `Record ADR` / `No ADR`. Keep `Show Option N` and `Suggest Other Options` for navigation.

`Yes`, `Accept recommendation`, or `Choose this` means accept the active recommendation as the answer. Record a one-line decision note, update docs immediately if the decision resolves domain language or an ADR-worthy choice, then continue to the next unresolved question.

`Show Option N` means show that numbered candidate using the same setup, demo, and tool flow. It is option navigation for the current question, not permission to defer the question or move to a later discussion point.

If the user navigates backward with `Show Option N` and then accepts that previous candidate, double-confirm before accepting it.

`Suggest Other Options` means the agent should think of additional candidate answers, add them to the candidate list, rank them against the existing options, then show the best new active recommendation with the same setup/demo/tool flow. It does not mean asking the user for custom text.

If the user wants to provide their own answer, use the question tool's freeform custom-answer input. Restate the custom answer as a precise decision and ask for confirmation before recording or writing docs.

If the custom answer appears to select an existing candidate by number, label, or short name, treat it as navigation to that candidate instead of a freeform decision. Show that candidate as the active recommendation using the same setup/demo/tool flow, then ask for confirmation with `Yes`, `Accept recommendation`, or `Choose this` before recording or writing docs.

Rank candidate answers before presenting them. The first candidate should be the best recommendation based on user goals, repo facts, existing docs, domain language, and engineering tradeoffs. Do not make all choices seem equal.

</lazy-choice-flow>

<supporting-info>

## Domain awareness

During codebase exploration, also look for existing documentation:

### File structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
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

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

</supporting-info>
