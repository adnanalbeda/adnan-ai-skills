---
name: grill-me
description: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree. Use when user wants to stress-test a plan, get grilled on their design, or mentions "grill me".
---

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer. For each answer, explain the decision effect and provide example or demo.

For every question, suggest at least two concrete answer options. Mark exactly one as recommended, put it first unless repo evidence makes another ordering clearer, and use this format:

````md
1. option-a (recommended)
description: <what this chooses>
example:
```
<small concrete example or demo>
```
reason: <why this is recommended>

2. option-b
description: <what this chooses>
example:
```
<small concrete example or demo>
```
reason: <why someone might choose this instead>
````

Ask the questions one at a time.

If a question can be answered by exploring the codebase, explore the codebase instead.
