---
name: lazy-grill
description: Relentless planning interview like grill-me, but optimized for lazy/choice-driven answers. Use when the user wants rigorous grilling without typing open-ended answers, asks for lazy grilling, choice-driven grilling, or says not to make them write answers.
---

# Lazy Grill

Interview the user relentlessly about every aspect of a plan until reaching shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one by one.

This is a duplicate of `grill-me` with a different interaction style: keep the same rigor, but make every answer path as low-effort as possible for the user.

## Start rule

First, ask whether the user really wanted to use this skill, or meant to use `grill-with-docs` instead.

Use `grill-with-docs` when the plan should be challenged against existing repo docs, domain language, `CONTEXT.md`, ADRs, or implementation conventions. Use `lazy-grill` when the user wants a conversational design interrogation with lazy choice navigation.

## Core rules

- Ask one design question at a time.
- For each question, provide your recommended answer.
- If a question can be answered by exploring files or code, explore first instead of asking the user.
- If the question/choice tool is available, use it for the active recommendation flow.
- Convert open-ended questions into 2-4 concrete candidate answers plus an Other/custom-answer path whenever possible.
- Keep grilling depth equal to `grill-me`; only the output and answer mechanics are easier.

## Lazy choice flow

Before calling the question/choice tool, write a short setup that includes:

- The current question.
- All candidate answers with short tradeoffs.
- The active recommended answer.
- The smallest useful demonstration for the active recommendation, such as a tiny code sample, folder tree, state flow, architecture sketch, before/after behavior, or example prompt.

Then call the question/choice tool with navigation choices for the active recommendation:

- First candidate: `Yes`, `Next`, and `Suggest Other Options`.
- Middle candidate: `Yes`, `Next`, `Prev`, and `Suggest Other Options`.
- Last candidate: `Yes`, `Prev`, and `Suggest Other Options`. Do not wrap to the first option. If the user rejects the last candidate, ask them to suggest other options.

If the design question itself is a yes/no decision, avoid confusing labels like "Yes to yes". Rename the acceptance action to `Accept recommendation` or `Choose this`, and phrase candidates as concrete outcomes such as `Enable it` / `Do not enable it`, `Create it` / `Skip it`, or `Use X` / `Do not use X`. Keep `Next`, `Prev`, and `Suggest Other Options` for navigation.

`Yes`, `Accept recommendation`, or `Choose this` means accept the active recommendation as the answer. Record a one-line decision note, then immediately continue to the next unresolved question.

`Next` means show the next recommended candidate using the same setup/demo/tool flow.

`Prev` means return to the previous recommended candidate. If the user then chooses `Yes` on that previous candidate, double-confirm before accepting it.

`Suggest Other Options` means the agent should think of additional candidate answers, add them to the candidate list, rank them against the existing options, then show the best new active recommendation with the same setup/demo/tool flow. It does not mean asking the user for custom text.

If the user wants to provide their own answer, use the question tool's freeform custom-answer input. Restate the custom answer as a precise decision and ask for confirmation before recording it.

If the custom answer appears to select an existing candidate by number, label, or short name, treat it as navigation to that candidate instead of a freeform decision. Show that candidate as the active recommendation using the same setup/demo/tool flow, then ask for confirmation with `Yes` before recording it. Example: if candidate 1 is active and the user types `3` in the custom-answer input, show candidate 3 as the active recommendation and ask the user to confirm it.

## Recommendation behavior

Rank candidate answers before presenting them. The first candidate should be the best recommendation based on user goals, known constraints, repo facts, and engineering tradeoffs.

Do not make all choices seem equal. Be opinionated, but make it easy to move to the next best option.

For each candidate answer, include enough reasoning for the user to accept or reject it quickly. Keep the active recommendation explanation short; put deeper tradeoffs in the pre-tool list.

## State tracking

Maintain a running internal decision log while grilling:

- Accepted decisions.
- Rejected candidates when rejection changes later recommendations.
- Open questions and dependencies.
- Repo facts discovered through exploration.

Use accepted decisions to shape later questions. Do not re-ask settled decisions unless a later answer creates a conflict.

## Completion

Stop when the plan has no unresolved blocking questions and both assistant and user share the same understanding of scope, constraints, tradeoffs, and next steps.

At the end, summarize:

- Accepted decisions.
- Remaining risks or assumptions.
- Recommended next action.
