# Agents Global Instructions

## NPM

- Never use `npm` or `npx`.
- Always use `pnpm` instead of `npm`.
- Always use `pnpx` instead of `npx`.
- The only exception is if there's a `package.json` which strict the engine or package manager to npm.

## Prompt Polishing

- Load `prompt-terminology-polisher` when the prompt seems fuzzy or require thinking to understand intent.
- Preserve the user's intent and constraints. Improve grammar and technical wording, and flag uncertain terminology instead of silently guessing.

## Caveman

- Load and use `caveman` with mode `ultra` at session start before real work, if skill tool exists.
- Do not reload if already active.
- Avoid `caveman` during grilling (`grill-me`, `grill-with-docs`). Keep questions clear. Auto resume after.
- Use normal clear English for security warnings, irreversible confirmations, or any step where fragments may confuse. Resume after.
- Use `caveman` by default for reasoning, progress, final replies.
- Stop only when user asks for normal mode or says stop caveman.

<!-- cavecrew -->
- Load `cavecrew` when delegating, user asks for subagents/cavecrew/context saving, or compressed agent output helps.
- Use [cavecrew-investigator](./agents/cavecrew-investigator.md) for read-only locating: definitions, callers, refs, usage lists, broad `path:line` searches.
- Use [cavecrew-builder](./agents/cavecrew-builder.md) for clear 1-2 file edits after target files known.
- Use [cavecrew-reviewer](./agents/cavecrew-reviewer.md) for bug-focused diff/file review when findings-only enough.
- Use vanilla `Explore` or reviewers when prose, architecture, rationale, or alternatives matter.
- Use main thread for one-liners, tiny fixes, new features, 3+ file refactors, cross-cutting changes.
- Broad search: run 2-3 `cavecrew-investigator` agents in parallel with different angles, then merge results.
- Never use `cavecrew-builder` before files found. Never force cavecrew when terse output hurts clarity.
<!-- cavecrew -->

<!-- context7 -->
Use Context7 MCP to fetch current documentation whenever the user asks about a library, framework, SDK, API, CLI tool, or cloud service -- even well-known ones like React, Next.js, Prisma, Express, Tailwind, Django, or Spring Boot. This includes API syntax, configuration, version migration, library-specific debugging, setup instructions, and CLI tool usage. Use even when you think you know the answer -- your training data may not reflect recent changes. Prefer this over web search for library docs.

Do not use for: refactoring, writing scripts from scratch, debugging business logic, code review, or general programming concepts.

## Steps

1. Always start with `resolve-library-id` using the library name and the user's question, unless the user provides an exact library ID in `/org/project` format
2. Pick the best match (ID format: `/org/project`) by: exact name match, description relevance, code snippet count, source reputation (High/Medium preferred), and benchmark score (higher is better). If results don't look right, try alternate names or queries (e.g., "next.js" not "nextjs", or rephrase the question). Use version-specific IDs when the user mentions a version
3. `query-docs` with the selected library ID and the user's full question (not single words)
4. Answer using the fetched docs
<!-- context7 -->

## Planning And Grilling Research

- On `grill-*`: don not trust prompt alone. Find hidden violations, conflicts, weak assumptions, antipatterns.
- Research docs/conventions/specs first. Challenge plan with evidence. Surface risks before proceed.
