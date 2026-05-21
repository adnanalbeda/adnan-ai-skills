---
name: adnan-ai-skills-guide
description: Catalog of this repo's available skills and configured MCPs, with guidance for choosing the right capability before work. Use when discovering available skills/MCPs, deciding which skill to load, choosing between overlapping workflows, or updating this repo's skill/MCP inventory.
---

# Adnan AI Skills Guide

Use this skill to discover available repo skills and MCPs before choosing a workflow. Prefer the specialized skill or MCP when a task matches its trigger; otherwise use normal codebase exploration.

## Selection Rules

- Load the most specific matching skill before substantive work.
- If multiple skills fit, prefer planning/grilling skills for requirements, diagnosis skills for bugs, implementation-plan skills for existing plans, and domain skills for specific frameworks/libraries.
- Use MCPs for first-party or live ecosystem docs/tools when they are more authoritative than local memory.
- When adding, removing, or renaming any skill or MCP in this repo, update this guide in the same change.

## Workflow Skills

- `plan-feature`: end-to-end feature planning from idea to PRD/spec/issues/Jira docs.
- `lazy-plan-feature`: same planning pipeline, but choice-driven and low-effort for user answers.
- `post-grill`: same downstream artifact pipeline as `lazy-plan-feature`, but starts after grilling is complete and does not grill again.
- `implement-feature-plan`: execute an existing local PRD/spec/issue plan.
- `to-prd`: turn conversation context into a local PRD.
- `to-spec`: turn a PRD, plan, issue, or context into an implementation spec.
- `to-issues`: break a plan/spec into independently grabbable issue drafts.
- `to-jira`: turn a prepared spec into Jira and Confluence-ready artifacts.
- `write-a-skill`: create or revise agent skills with proper structure.

## Grilling And Planning Pressure

- `grill-me`: rigorous design interrogation, one question at a time.
- `lazy-grill`: same rigor as `grill-me`, but with recommendation-first choice navigation.
- `grill-with-docs`: challenge a plan against repo docs, domain language, and ADRs.
- `lazy-docs`: docs-aware grilling with lazy choice flow and inline doc updates.
- `prototype`: build throwaway prototypes for state, logic, or UI exploration.
- `zoom-out`: get broader context when a code area or architecture is unclear.

## Engineering Execution

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
