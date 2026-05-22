---
name: adnan-ai-skills-guide
description: Skill and MCP selection guide that maps user intent to the right workflow, domain skill, communication mode, or documentation source. Use when choosing between skills, resolving overlapping triggers, discovering configured MCPs, or updating the skill/MCP inventory.
---

# Adnan AI Skills Guide

Use this skill to discover available repo skills and MCPs before choosing a workflow. Prefer the specialized skill or MCP when a task matches its trigger; otherwise use normal codebase exploration.

## Selection Rules

- Use `adnan-ai-skills-guide` itself when discovering available repo skills/MCPs or updating this inventory.
- Load the most specific matching skill before substantive work.
- If multiple skills fit, prefer planning/grilling skills for requirements, diagnosis skills for bugs, implementation-plan skills for existing plans, and domain skills for specific frameworks/libraries.
- If the user asks for `/oh-my-ai-skills`, `oh-my-ai-skill`, full docs-aware planning through implementation, Ralph Loop, or no human stage gates, prefer `oh-my-ai-skill` over `plan-feature` and direct `implement-feature-plan`.
- If the user asks for ultrawork, ultra focused work, or bounded uninterrupted execution without the full planning artifact pipeline, prefer `ufw`.
- If the user asks for `hyperplan`, hostile planning, adversarial critique, red-team planning, or cross-critique, load `hyperplan` before creating or executing a plan.
- Use MCPs for first-party or live ecosystem docs/tools when they are more authoritative than local memory.
- When adding, removing, or renaming any skill or MCP in this repo, update this guide in the same change.

## Workflow Skills

- `plan-feature`: end-to-end feature planning from idea or resolved decisions to PRD/spec/issues/Jira docs, with interactive, low-effort, or hands-off modes.
- `oh-my-ai-skill`: local-only uninterrupted docs-aware grilling, subagent council, PRD/spec/issues review gates, and Ralph-loop implementation.
- `ufw`: Ultra Focused Work loop for bounded uninterrupted execution with compact state and verification.
- `implement-feature-plan`: execute existing local PRD/spec/issue plans from `plan-feature` or `oh-my-ai-skill`.
- `to-prd`: turn conversation context into a local PRD.
- `to-spec`: turn a PRD, plan, issue, or context into an implementation spec.
- `to-issues`: break a plan/spec into independently grabbable issue drafts.
- `to-jira`: turn a prepared spec into Jira and Confluence-ready artifacts.
- `write-a-skill`: create or revise agent skills with proper structure.

## Grilling And Planning Pressure

- `lazy-grill`: rigorous design interrogation with normal one-question flow or recommendation-first choice navigation.
- `lazy-docs`: docs-aware grilling with optional choice flow, glossary challenge, inline doc updates, ADR discipline, and decision-tree tracking.
- `grill-me`: legacy conversational grilling;
- `grill-with-docs`: legacy docs-aware grilling;
- `hyperplan`: hostile planning with five adversarial lenses and fallback from team tools to task subagents or sequential simulation.
- `prototype`: build throwaway prototypes for state, logic, or UI exploration.
- `zoom-out`: get broader context when a code area or architecture is unclear.

## Engineering Execution

- `council`: structured multi-role analysis for hard decisions, contradictions, trade-offs, and review gates.
- `diagnose`: reproduce, minimize, hypothesize, instrument, fix, and regression-test bugs or performance regressions.
- `tdd`: red-green-refactor feature or bug work when test-first development is requested.
- `improve-codebase-architecture`: find refactoring and architecture improvements using repo docs and ADRs.
- `agent-breadcrumbs`: maintain recovery breadcrumbs during long or interruption-prone work.
- `connection-recovery`: reconstruct interrupted work from memory, breadcrumbs, files, logs, and git state.

## Communication And Context Compression

- `caveman`: compressed default communication mode.
- `caveman-help`: one-shot command/help card for caveman modes.
- `caveman-commit`: concise Conventional Commit message generation.
- `cavecrew`: decide when to delegate to compressed subagents.

## Domain Skills

- `litebus-dotnet`: LiteBus mediator, CQS, DDD messages, handlers, and pipelines.
- `quartz-dotnet`: Quartz.NET jobs, triggers, cron schedules, hosting, persistence, clustering, and misfires.
- `scalar-dotnet`: Scalar.AspNetCore API reference UI and OpenAPI route/configuration guidance.
- `storybook-angular`: Angular Storybook setup, stories, docs, controls, builders, and addons.

## Configured MCPs

- `dotnet`: Microsoft Learn MCP at `https://learn.microsoft.com/api/mcp`; use for official Microsoft/.NET/Azure docs and code samples.
- `angular`: local Angular CLI MCP via `pnpx @angular/cli mcp`; use for Angular project discovery, best practices, docs, and migrations.
- `dx`: DevExpress docs MCP at `https://api.devexpress.com/mcp/docs`; use for DevExpress product documentation.
- `aspire`: local Aspire MCP via `aspire agent mcp`; use for Aspire resources, logs, traces, diagnostics, integrations, and docs.

## Maintenance Checklist

- New skill: add its name, trigger, and one-line purpose here.
- Changed skill behavior: update the relevant guide entry if selection criteria changed.
- New MCP: add config name, source, and when to use it.
- Removed skill/MCP: remove or mark unavailable here.
- Keep this file concise; detailed behavior belongs in each skill's own `SKILL.md`.
