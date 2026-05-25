# Decision Tree Format

Decision-tree files live in `docs/grill-answers-tree/` and use sequential numbering: `0001-topic-answers.md`, `0002-next-topic-answers.md`, etc.

Create `docs/grill-answers-tree/` lazily when the first concrete answer is recorded.

## Template

```md
# {Topic} Decision Tree

## Session

- Topic: {short topic name}
- Started: {YYYY-MM-DD}
- Status: open
- Mode: unattended agent-selected answers
- Estimated No. of Question: {approx no. of questions - estimate without deep think}
- Actual No. of Questions: {add real number of question after the session is over for stats compare}

## Aspects

- **{Aspect name}**: open (Est: {approx no. of questions - estimate without deep think})
- **{Future aspect}**: deferred until {what must be resolved first}
- **{Resolved aspect}**: resolved (Est: {approx no. of questions - estimated without deep think} - Act: {add real number of question after the aspect discussion is over for stats compare})

## Decision Tree

### {Aspect name}

#### Q1. {Question}

Answer: {Concrete selected answer}

Selected by: agent comparison

Evidence: {Optional strongest evidence, preferably paths/lines when available}

Notes: {Optional clarification, only when needed}

Depends on: {Optional earlier answer or aspect this question requires}

Unlocks: {Optional next question or aspect opened by this answer}

Deferred because: {Optional branch or aspect skipped because of this answer}
```

## Rules

- **Record only concrete answers.** Do not write tentative exploration, rejected live options, or transcript-like discussion.
- **Keep entries minimal.** Each entry needs `Question`, `Answer`, and `Selected by`; add `Evidence`, `Notes`, `Depends on`, `Unlocks`, or `Deferred because` only when they clarify real branching.
- **Use aspects as the main structure.** When the topic has two or more aspects, list all known aspects up front and mark each as `open`, `deferred`, or `resolved`.
- **Track one active aspect.** Mark the current aspect as `open`; mark later aspects as `deferred until ...` until they become in scope.
- **Update after each selected answer.** After the comparison subagent selects a concrete answer and the main interviewer accepts it, append or update the relevant Q/A entry before asking the next question.
- **Prefer durable wording.** Use terms and decisions that will still make sense after the conversation ends; avoid references like "as above" or "the current option".
- **Do not duplicate ADRs.** If a decision also deserves an ADR, keep this file to the Q/A record and create the ADR separately using `ADR-FORMAT.md`.

## Numbering

Scan `docs/grill-answers-tree/` for the highest existing number and increment by one.

Use a short kebab-case topic slug followed by `-answers.md`:

- `0001-event-sourced-orders-answers.md`
- `0002-postgres-for-write-model-answers.md`

## Status

- `open`: currently being discussed or not fully resolved.
- `deferred`: intentionally out of scope until another aspect or dependency is resolved.
- `resolved`: all currently known questions for the aspect have concrete answers.
