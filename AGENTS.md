# Agents Global Instructions

## NPM

- Never use `npm` or `npx`.
- Always use `pnpm` instead of `npm`.
- Always use `pnpx` instead of `npx`.
- The only exception is if there's a `package.json` which strict the engine or package manager to npm.

## Caveman

- Load the `caveman` skill at session start, before substantive work, when the skill tool is available.
- Do not reload `caveman` every turn if it is already active in the current conversation.
- Use `caveman` as the default compression style for reasoning, progress updates, and final responses.
- Avoid caveman style while grilling is in process (`grill-me`,  `grill-with-docs`, `plan-feature`, `lazy-grill`, `lazy-docs`, lazy-plan`) so questions stay clear and precise. Resume caveman after grilling is over.
- Temporarily use normal clear English for security warnings, irreversible-action confirmations, or any step where caveman-style fragments could be misread. Resume `caveman` after the clear part.
- Stop using `caveman` only when the user explicitly asks for normal mode or says to stop caveman.

## Cavecrew

- Load the `cavecrew` skill when considering subagent delegation, when the user asks to delegate/spawn subagents/use cavecrew/save context, or when compressed subagent output would preserve main-context budget.
- Use `cavecrew-investigator` for read-only code location tasks: definitions, callers, references, usage lists, and broad searches where terse `path:line` output is enough.
- Use `cavecrew-builder` for obvious surgical edits scoped to 1-2 files after the target files are known.
- Use `cavecrew-reviewer` for bug-focused diff or file review when findings-only output is enough.
- Prefer vanilla `Explore` or reviewer agents when prose, architecture commentary, rationale, or alternatives are needed.
- Prefer the main thread for one-line answers, small obvious actions, new features, 3+ file refactors, or cross-cutting changes.
- For broad investigation, run 2-3 `cavecrew-investigator` agents in parallel with different search angles, then aggregate results in the main thread.
- Do not use `cavecrew-builder` before locating the relevant files, and do not force cavecrew when its terse output would make user-facing results unclear.

## Planning And Grilling Research

- When any `grill-*`, `lazy-*`, or `plan-feature` skill is used, do not treat clear user instructions as enough by themselves. The user may be asking for help spotting whether the prompt or plan implies guideline violations, spec conflicts, weak assumptions, or known antipatterns.
- Research relevant repo docs, existing conventions, official standards/specs, and known antipatterns before accepting, refining, or converting the prompt/plan. Challenge the plan against that evidence and surface conflicts or risks before proceeding.

## Breadcrumbs And Connection Recovery

- Load `agent-breadcrumbs` at the start of long-running or interruption-prone work: planning pipelines, implementation tasks, multi-file edits, subagent orchestration, long tests/builds, reviews, or documentation generation.
- Store breadcrumbs in the current repo/workspace at `.agent/state/breadcrumbs/<agent-id>.md`.
- Use runtime/tool agent id for `<agent-id>` when available; otherwise use timestamp plus short task slug, for example `2026-05-19-1832-lazy-plan-feature.md`.
- Maintain one breadcrumb file per concurrent agent/task. Never share one breadcrumb file between parallel agents.
- When a breadcrumb is active, keep this compact pointer in session context: `Recovery breadcrumb: .agent/state/breadcrumbs/<agent-id>.md`.
- Repeat the pointer after first write, at phase changes, before long commands/subagent calls, before stopping for a user decision, and in handoff or recovery summaries. Do not repeat it in every message.
- Update breadcrumbs at task start, after each meaningful completed step, before risky phase changes, before long commands/subagent calls, after subagents return, and before stopping for a blocker or user decision.
- Breadcrumbs must include: `Intent`, `Goal`, `Current phase`, `Last completed step`, `Active files/artifacts`, `Next safe action`, `Blockers`, `Verification status`, and `Last updated`.
- Keep breadcrumbs concise. They are recovery indexes, not transcripts. Never store secrets, credentials, tokens, or private data.
- Do not commit breadcrumb files unless the user explicitly asks.
- Load `connection-recovery` when the user says the connection dropped, the agent stopped mid-task, work needs to be picked back up, or current context may be incomplete after interruption.
- During recovery, use current conversation memory first, parse any `Recovery breadcrumb:` pointer, verify it matches the current request, then fall back to breadcrumb discovery, files, artifacts, worktree state, and relevant logs/tests before continuing.
- Recovery must summarize reconstructed state and ask before continuing. Do not silently continue after reconnect.
