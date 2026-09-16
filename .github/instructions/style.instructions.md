---
description: 'Tailwind CSS v4 styling patterns and dark theme guidelines'
applyTo: '**/*.{astro,css}'
---

# Tailwind CSS Instructions and TypeScript Formatting

## Tailwind CSS v4 Configuration

This project uses Tailwind CSS v4.1.14 via the `@tailwindcss/vite` plugin.

### Global CSS Setup

- Import Tailwind in `global.css`: `@import "tailwindcss";`
- No separate `tailwind.config.js` file is used
- Configuration is handled through the Vite plugin

## Dark Theme Styling

ALL UI components MUST use dark theme colors:

### Color Palette

- Background colors: `bg-slate-800`, `bg-slate-900`, `bg-slate-950`
- Text colors: `text-slate-100`, `text-slate-200`, `text-slate-300`
- Border colors: `border-slate-700`, `border-slate-600`
- Accent colors for hover/focus states

### Common Patterns

- Cards and containers: `bg-slate-800 rounded-xl p-6 shadow-lg`
- Hover effects: `hover:bg-slate-700 transition-colors duration-200`
- Borders: `border border-slate-700`
- Gradients for visual interest: `bg-gradient-to-br from-slate-800 to-slate-900`
- Backdrop effects: `backdrop-blur-sm bg-slate-900/50`

### Responsive Design

- Use responsive prefixes: `sm:`, `md:`, `lg:`, `xl:`
- Mobile-first approach
- Ensure readability on all screen sizes

## Utility Classes

- Prefer utility classes over custom CSS when possible
- Use semantic grouping: layout, spacing, colors, typography
- Keep utility combinations readable and maintainable

## Modern UI Patterns

- Rounded corners: `rounded-lg`, `rounded-xl`, `rounded-2xl`
- Smooth transitions: `transition-all duration-200 ease-in-out`
- Shadows for depth: `shadow-md`, `shadow-lg`, `shadow-xl`
- Focus states for accessibility: `focus:ring-2 focus:ring-blue-500`

## TypeScript Formatting and Style

### Explicit Types Required

All function parameters and return types must have explicit type annotations. This applies to:
- Exported functions in `db/` and `src/lib/`
- Public methods in classes or modules
- Test utilities and helpers

```ts
// ✅ Good: Explicit types
export async function getAllGames(db: Database): Promise<Game[]> {
  // …
}

export function ratingFromTitle(title: string): number {
  // …
}

// ❌ Bad: Missing return type
export async function getAllGames(db: Database) {
  // …
}
```

### Type Organization

- Import types at the top of the file after other imports
- Use `type` keyword for type-only imports: `import type { Game } from '../../types'`
- Define `Props` interfaces in frontmatter for Astro components (see `astro.instructions.md`)
- Keep types in `src/types/` for shared app-facing types (Game, Publisher, Category)

```ts
// ✅ Good: Types organized clearly
import { getAllGames, getGameById } from './games';
import type { Game, Publisher, Category } from '../types';

export async function buildGamePage(db: Database, id: number): Promise<PageData> {
  // …
}
```

### Naming Conventions

- Interfaces and types: PascalCase (`Game`, `Publisher`, `GameWithRelations`)
- Functions and variables: camelCase (`getAllGames`, `gameId`)
- Constants: UPPER_SNAKE_CASE for truly constant values
- Use descriptive names that convey intent — type-checking makes code more readable, not less

### Async/Await

- Prefer async/await over `.then()` chains — it reads more naturally
- Always await async operations; don't omit the `await` keyword
- Use `satisfies` for type narrowing when helpful, but prefer explicit types

```ts
// ✅ Good
export const getStaticPaths = (async () => {
  const ids = await getAllGameIds(getDatabase());
  return ids.map((id) => ({ params: { id: String(id) } }));
}) satisfies GetStaticPaths;

// ❌ Avoid
const getStaticPaths = async () => {
  const ids = getAllGameIds(getDatabase()); // Missing await
  // …
};
```

### Null / Undefined

- Prefer explicit nullable types: `function | null` instead of optional `function?`
- Use type narrowing (guards) to handle nulls safely
- Never suppress type errors with `any` — use `unknown` with proper narrowing if needed

```ts
// ✅ Good: Explicit and safe
const game = await getGameById(db, id);
if (game === null) {
  return notFound();
}

// ❌ Avoid: any suppresses safety
const game: any = await getGameById(db, id);
```

### Type Checking

- Run `npm run typecheck` regularly (pure TypeScript, `db/`, `src/lib/`)
- Run `npm run typecheck:astro` for `.astro` files
- Run `npm run typecheck:all` for both — CI runs this on pull requests
- TypeScript 7 (`tsgo`, via `@typescript/native-preview`) type-checks pure TypeScript; classic TypeScript v6 type-checks `.astro` files and ESLint; do not bump the classic package until both tools support the native compiler's API
