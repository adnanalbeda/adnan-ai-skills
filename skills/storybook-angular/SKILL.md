---
name: storybook-angular
description: Storybook guidance for Angular projects, avoiding React-specific Storybook assumptions. Use when setting up, configuring, writing, testing, documenting, or troubleshooting Storybook stories for Angular components, Angular workspaces, Angular CLI builders, Compodoc, moduleMetadata, applicationConfig, controls, args, decorators, or Storybook Angular addons.
---

# Storybook Angular

Use Angular-specific Storybook patterns. Do not use React JSX examples, React hooks, React render functions, or React-only addons unless the project explicitly contains React.

## Official Sources

- Storybook docs: https://storybook.js.org/docs/
- Angular framework docs: https://storybook.js.org/docs/get-started/frameworks/angular
- Writing stories: https://storybook.js.org/docs/writing-stories
- Args and controls: https://storybook.js.org/docs/writing-stories/args
- Decorators: https://storybook.js.org/docs/writing-stories/decorators
- Autodocs: https://storybook.js.org/docs/writing-docs/autodocs

Check the installed Angular and Storybook versions before applying migration or config advice.

## Quick Start

Follow the repo package manager. In this environment, use `pnpm`/`pnpx`, never `npm`/`npx`.

```powershell
pnpm create storybook@latest
ng run <project-name>:storybook
ng run <project-name>:build-storybook
```

For Angular workspaces, prefer Angular builder targets in `angular.json` over direct `storybook dev` scripts when those targets exist.

## Story Example

```ts
import type { Meta, StoryObj } from '@storybook/angular';

import { ButtonComponent } from './button.component';

const meta = {
  component: ButtonComponent,
  tags: ['autodocs'],
} satisfies Meta<ButtonComponent>;

export default meta;
type Story = StoryObj<ButtonComponent>;

export const Primary: Story = {
  args: {
    label: 'Save',
    disabled: false,
  },
};
```

Use `args` for Angular `@Input()` values. Use Storybook Actions or explicit output handling for `@Output()` events.

## Angular Dependencies

- Use `moduleMetadata` when a component needs Angular imports, declarations, or local providers.
- Use `applicationConfig` for application-wide providers, standalone provider APIs, animations, routing, HTTP setup, or providers normally passed to `bootstrapApplication`.
- Use Compodoc for Angular API extraction and controls/docs metadata.
- Add useful JSDoc to `@Input()` and `@Output()` members for generated docs.
- In `.storybook/preview.ts`, use `setCompodocJson` from `@storybook/addon-docs/angular` when the project generates `documentation.json`.

## Workflow

1. Identify Angular version, Storybook version, and whether the component is standalone or NgModule-based.
2. Inspect existing `.storybook/main.ts`, `.storybook/preview.ts`, `angular.json`, and nearby `*.stories.ts` files.
3. Preserve existing builder choice, addon list, decorators, global styles, and project conventions.
4. Prefer colocated `*.stories.ts` files beside Angular components unless the repo uses a central stories folder.
5. Keep stories deterministic and isolated from live backend calls.

## Common Pitfalls

- Do not copy React examples from Storybook docs into Angular stories.
- Do not use JSX in Angular stories.
- Do not use React hook examples for interactive state.
- Do not bypass Angular DI with hand-rolled service construction unless the component already does so.
- Do not forget global styles, assets, or `stylePreprocessorOptions` when visual output differs from the app.
- Do not assume direct `storybook dev` scripts are correct in Angular CLI workspaces; prefer `ng run` targets when configured.
