---
name: connection-recovery
description: Recover after connection loss or interrupted agent work by using current conversation memory, agent breadcrumbs, worktree state, artifacts, and verification results to reconstruct where work stopped and ask before continuing.
---

# Connection Recovery

Use when the user says the connection dropped, the agent stopped mid-task, work needs to be picked back up, or the current context may be incomplete after an interruption.

## Recovery Order

1. Use current conversation memory first when it is available.
2. Verify memory against files, artifacts, and worktree state before editing or continuing.
3. Read relevant breadcrumbs from `.agent/state/breadcrumbs/<agent-id>.md` when present.
4. If no breadcrumb exists, reconstruct from available local evidence: user prompt, changed files, PRD/spec/issues, docs, logs, tests, and git status/diff when inside a git repo.
5. Ask the user only when state remains ambiguous or the next action is risky.

## Breadcrumb Discovery

Look for repo-local breadcrumbs at:

```md
.agent/state/breadcrumbs/*.md
```

If multiple breadcrumb files exist:

- Prefer the one matching the current user goal or referenced artifact.
- Otherwise prefer the most recently updated file.
- If two or more plausible breadcrumbs conflict, summarize candidates and ask which to recover.

## Recovery Summary Then Ask

After reconstructing state, do not continue silently. Show a concise recovery summary and ask before taking the next action.

The summary must include:

- Recovered goal.
- Current phase.
- Last completed step.
- Active files/artifacts.
- Verification status.
- Recommended next safe action.
- Any blockers or ambiguity.

Use the question/choice tool when available. Put `Yes` first for the recommended next safe action, then `Next` for an alternate safe action when one exists, and `Other` for custom direction.

## Safety Rules

- Never assume partial edits are correct. Re-read touched files before continuing.
- Never overwrite or revert changes you did not make unless the user explicitly asks.
- Never run destructive commands, publish remotely, push branches, create PRs, or commit unless the user explicitly asks for that action.
- If recovery finds a failed/partial command, inspect the result and choose the smallest safe next step.
- If recovery finds unverified code changes, recommend verification before broader work.

## Reconstruction Checklist

Use only the relevant checks for the task:

- Current conversation memory: user goal, accepted decisions, last visible action.
- Breadcrumb: intent, phase, last completed step, next safe action, blockers.
- Worktree: changed/untracked files, staged changes, recent diffs.
- Planning artifacts: PRD, spec, issue drafts, Jira/Confluence docs.
- Implementation artifacts: touched source files, tests, generated files, logs.
- Verification: last test/build command and outcome, skipped verification, known failures.

## Handoff Back To Work

After the user approves the recommended next action, continue using the relevant original skill or workflow. If the recovered task is long-running, load `agent-breadcrumbs` and update the breadcrumb before continuing.

If the next action is unclear, ask one lazy, bounded question with the recommended path first.
