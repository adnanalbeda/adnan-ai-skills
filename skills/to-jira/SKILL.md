---
name: to-jira
description: Turn a prepared implementation spec into local Jira artifacts and Confluence documentation. Use when user asks for Jira tickets, an epic/story/task breakdown, or Confluence publish-ready docs after a spec exists.
---

# To Jira

Generate local files ready to copy or import into Jira and Confluence after a spec is prepared.

## Local-only rule

All output is local by default. Do NOT create, update, push, or publish anything to Jira, Confluence, GitHub, GitLab, or other remote services unless the user explicitly asks for that specific remote action.

## Recovery breadcrumbs

For non-trivial Jira/Confluence draft generation, load `agent-breadcrumbs` and update `.agent/state/breadcrumbs/<agent-id>.md` after reading the spec, creating the Epic/Story/Task breakdown, writing Confluence docs, and saving local files so `connection-recovery` can reconstruct progress after interruption.

## Inputs

Start from a prepared implementation spec. If no spec exists, load `to-spec` first or ask the user for the spec path.

Read linked PRDs, issue drafts, and source references if the spec points to them. Explore the codebase only to resolve names, constraints, and dependencies that affect ticket quality.

## Tags

Use these tags in Jira issue titles when appropriate: `[FE]`, `[BE]`, `[MOB]`, `[UI]`.

- `[FE]` - frontend web work
- `[BE]` - backend, API, data, jobs, integrations, infra-adjacent work
- `[MOB]` - mobile app work
- `[UI]` - visual design, UX, copy, layout, design-system work

Use multiple tags only when one issue genuinely spans areas. Prefer splitting mixed work into separate tagged tasks when dependencies allow it.

## Local file storage

Store files in the current repo, not in global agent config. If the repo already has Jira or Confluence draft conventions, follow them.

If generated from a spec, group files under `docs/jira/<source-spec-name>/`, where `<source-spec-name>` is the spec filename without `.md`. Example: `docs/specs/2026-05-19-checkout-flow.md` produces `docs/jira/2026-05-19-checkout-flow/`.

If there is no source spec file, create files lazily under `docs/jira/YYYY-MM-DD-kebab-title/`.

Default files:

- `epic.md`
- `stories/01-fe-kebab-title.md`
- `tasks/01-01-be-kebab-title.md`
- `confluence.md`
- `README.md` as an index when there are multiple stories or tasks

Use lowercase ASCII kebab-case for filenames. Keep Jira tags in titles, not filenames.

## Process

1. Read the spec and identify the user outcome, system boundaries, acceptance criteria, rollout needs, and testing plan.
2. Create one Epic for the whole feature unless the spec clearly covers multiple unrelated outcomes.
3. Break the Epic into Stories that are independently demoable or verifiable from a user or business perspective.
4. Break each Story into implementation Tasks. Tasks can be technical, but each must trace to a Story and acceptance criterion.
5. Add dependencies and blockers using local filenames until remote Jira keys exist.
6. Create `confluence.md` as publish-ready documentation for stakeholders and delivery teams.
7. Save everything locally. Do not publish remotely unless explicitly asked.

## Templates

Use [TEMPLATES.md](TEMPLATES.md) for Epic, Story, Task, and Confluence document formats.
