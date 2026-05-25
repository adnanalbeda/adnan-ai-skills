---
name: prompt-terminology-polisher
description: Polishes user prompts by fixing English, improving technical terminology, and preserving the user's intended request. Use when the user asks to fix, rewrite, polish, clarify, improve, or professionalize a prompt, especially when wording may use imprecise technical terms like "build variable" vs "environment variable".
---

# Prompt Terminology Polisher

Use this skill to turn rough prompts into clear, technically precise prompts without changing intent.

## Core Workflow

1. Identify intended task, audience, and domain from prompt plus surrounding context.
2. Fix grammar, spelling, punctuation, sentence flow, and unclear phrasing.
3. Improve technical terms when context makes intended term clear.
4. Preserve scope, tone, constraints, and requested outcome.
5. Flag uncertain terminology instead of silently guessing.

## Output Format

Default output:

```md
Polished prompt:
[rewritten prompt]

Terminology notes:
- [only include if a term was changed or is uncertain]
```

If the user asks for only the fixed prompt, return only the polished prompt.

## Terminology Rules

- Prefer the term that fits surrounding technical context, not literal wording.
- Replace vague or incorrect terms only when confidence is high.
- Explain important terminology changes briefly in `Terminology notes`.
- If multiple meanings are plausible, keep safest wording and add alternatives.
- Do not invent requirements, tools, platforms, versions, constraints, or implementation details.
- Do not over-formalize casual prompts unless user asks for professional tone.

## Common Term Corrections

Examples, not fixed replacements:

- `build variable` -> `environment variable` when context is app config, secrets, deployment, runtime config, `.env`, Docker, CI/CD runtime settings, or cloud hosting.
- `build variable` -> `build-time variable` when context is compiler flags, bundler config, static generation, CI build steps, or values baked into artifacts.
- `token` -> `API key`, `access token`, `refresh token`, or `JWT` only when context distinguishes which one.
- `server` -> `backend`, `API server`, `host`, or `deployment target` when context makes one meaning clearer.
- `database field` -> `column` for relational database tables; `property` or `field` for objects/documents.
- `login` -> `sign in` only if product terminology or style implies that wording.

## Confidence Handling

High confidence note:
`Changed "build variable" to "environment variable" because the prompt discusses deployment config and .env values.`

Medium/low confidence note:
`"Build variable" could mean environment variable or build-time variable. I used environment variable because the prompt mentions deployment, but confirm if you mean a CI/build pipeline value.`

## Examples

**Docker/runtime config**

Input: `fix this prompt: make app read build variable from docker and dont hardcode it`

Output:
```md
Polished prompt:
Update the app to read the value from an environment variable provided by Docker instead of hardcoding it.

Terminology notes:
- Changed "build variable" to "environment variable" because Docker runtime configuration usually passes values through environment variables.
```

**Vite/build config**

Input: `improve: add build variable to vite so api url available after npm run build`

Output:
```md
Polished prompt:
Configure Vite so the API URL is available as a build-time environment variable during the production build.

Terminology notes:
- Kept the build context but made the term more precise: Vite exposes build-time environment variables during `vite build`.
```

## Final Check

Before answering, verify: same intent, better English, more precise terms, no added scope, uncertainty flagged.
