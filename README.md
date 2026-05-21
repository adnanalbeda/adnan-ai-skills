# adnan-ai-skills

Personal AI agent skills and shared agent config, copied from the current global setup.

## Contents

- `skills/` - installable skill folders, each with `SKILL.md`
- `AGENTS.md` - global agent instructions
- `config/opencode/opencode.json` - OpenCode MCP config snapshot
- `config/agents/skill-lock.json` - captured global skill lock metadata

## AI-Made Skills

Skills my AI made in this repo include:

- `to-jira`
- `to-spec`
- `storybook-angular`
- `quartz-dotnet`
- `litebus-dotnet`
- `brainstorming`
- `agent-breadcrumbs`
- `connection-recovery`
- `lazy-*` (`lazy-docs`, `lazy-grill`, `lazy-plan-feature`)
- `post-grill`
- `implement-feature-plan`
- `plan-feature`

Other skills in `skills/` may also differ from official or upstream versions. This repo keeps my tailored variants for my own workflows, preferences, and agent setup.

## Breadcrumbs And Recovery

`agent-breadcrumbs` and `connection-recovery` are paired because they solve one interruption problem from two sides. `agent-breadcrumbs` writes small recovery notes during long work; `connection-recovery` reads those notes after a dropped connection or stopped agent, checks the worktree, summarizes current state, and asks before continuing.

Use `agent-breadcrumbs` at the start of long-running planning, implementation, review, docs, subagent, or verification work. It stores one concise note per active task at `.agents/state/breadcrumbs/<agent-id>.md`.

Use `connection-recovery` when work was interrupted, context may be incomplete, or the user says the connection dropped. It reconstructs from conversation memory, breadcrumbs, files, artifacts, git state, logs, and verification results, then recommends the next safe action.

## Use

Use this repo as the source for any skills CLI that installs from a local folder. The installable skill roots are under `skills/`.

Install from `skills.sh` / the `skills` CLI with:

```powershell
pnpx skills add adnanalbeda/adnan-ai-skills
```

This workspace did not have a `skills` or `skill` executable on PATH when checked, so exact local install syntax depends on the CLI you use.
