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

## Rollout and Backout
Rollout approach, migration notes, and backout path. Write "None" if not applicable.

## Testing Plan
How the Epic outcome will be verified.

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

## Testing Plan
How this Story will be verified.

## Rollout Notes
Story-specific rollout, migration, or release notes. Write "None" if not applicable.

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

## Blocked By
None - can start immediately

## Testing Plan
How this Task will be verified.

## Rollout Notes
Task-specific rollout, migration, or release notes. Write "None" if not applicable.

## Notes
Relevant constraints or prior art.
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
