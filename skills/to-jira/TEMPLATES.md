# To Jira Templates

## Epic

```md
# <Epic Title>

## Issue Type
Epic

## Summary
One sentence describing the feature outcome.

## Business Value
Why this matters and who benefits.

## Scope
What is included.

## Out of Scope
What is explicitly excluded.

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Child Stories
- `stories/01-fe-kebab-title.md`
```

## Story

```md
# [TAG] <Story Title>

## Issue Type
Story

## Parent
`../epic.md`

## User Story
As a <actor>, I want <capability>, so that <benefit>.

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Tasks
- `../tasks/01-01-be-kebab-title.md`

## Blocked By
None - can start immediately
```

## Task

```md
# [TAG] <Task Title>

## Issue Type
Task

## Parent Story
`../stories/01-fe-kebab-title.md`

## What To Do
Concrete implementation work for this task.

## Acceptance Criteria
- [ ] Criterion 1

## Notes
Relevant constraints, prior art, testing notes, or rollout notes.
```

## Confluence

```md
# <Feature Title>

## Overview
Short stakeholder-friendly explanation of the feature.

## Goals
- Goal 1

## User Impact
Who is affected and how behavior changes.

## Solution Summary
High-level system behavior without code-level detail.

## Delivery Plan
Epic, stories, tasks, dependencies, rollout, and testing summary.

## Open Questions
None
```
