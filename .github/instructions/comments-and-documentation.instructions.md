---
description: 'Comments and documentation standards: TSDoc, JSDoc, and code comments'
applyTo: 'db/**/*.ts,src/**/*.ts,**/*.astro'
---

# Comments and Documentation Standards

## Philosophy: Comment Why, Not What

Comments should explain **why** a piece of code exists or the reasoning behind a non-obvious decision. **Never restate what the code already says.** If a line of code is confusing, the code itself should be clearer — not just more documented.

### Examples

#### ❌ Bad: Comment restates the code

```ts
// Increment the counter by 1
counter++;

// Return the number of games
return games.length;

// Convert string to lowercase
const lowerName = name.toLowerCase();
```

#### ✅ Good: Comment explains reasoning or intent

```ts
// Deterministic seeding ensures static builds are reproducible across environments
counter++;

// Return count to detect when the game list is empty (allows conditional rendering)
return games.length;

// Match game titles case-insensitively for lookups
const lowerName = name.toLowerCase();
```

## TSDoc / JSDoc for Exported Functions

**Every exported function in `db/` and `src/lib/` must have a TSDoc/JSDoc comment** describing its purpose, parameters, return value, and any important side effects.

Use the following format:

```ts
/**
 * Retrieves all games ordered by title.
 * 
 * @param db - The database instance (injectable for testability).
 * @returns An array of games with related publisher and category data, ordered alphabetically by title.
 */
export async function getAllGames(db: Database): Promise<Game[]> {
  // …
}

/**
 * Generates a deterministic rating from a game title.
 * Used during seeding so static builds are reproducible.
 * 
 * @param title - The game title to rate.
 * @returns A number between 3.0 and 5.0, deterministically derived from the title.
 */
export function ratingFromTitle(title: string): number {
  // …
}
```

### Guidelines

- Place comments immediately above the function signature.
- Include `@param` tags for all parameters.
- Include `@returns` tag describing the return value and its semantics.
- Document non-obvious behavior, side effects, or constraints (e.g., determinism, database assumptions).
- For injectable `db` parameters, explicitly state: "The database instance (injectable for testability)."
- Use brief, complete sentences without trailing periods in tag descriptions.

## Astro Component Documentation

### Props Interface

**Every reusable `.astro` component must document its `Props` interface** so the component API is self-explanatory. Use a JSDoc comment above the `interface Props` definition:

```astro
---
/**
 * GameCard — displays a single game in a card layout.
 * 
 * Props:
 * - game: The game object with title, publisher, category, rating, and funding status.
 * - highlight: (Optional) Highlight the card with accent styling if true.
 */
interface Props {
  game: Game;
  highlight?: boolean;
}

const { game, highlight = false } = Astro.props;
---

<article class={highlight ? "border-blue-500" : ""}>
  <!-- … -->
</article>
```

### Guidelines

- Document the component's purpose in a brief line or two.
- List each prop with its type and any defaults or constraints.
- Use this format for consistency with TypeScript interfaces elsewhere in the codebase.
- Keep descriptions concise — a short phrase per prop is sufficient.

## Keeping Comments Current

**Treat outdated comments as bugs.** When code changes, update or delete related comments in the same change:

- If a comment no longer applies, delete it.
- If a comment is still relevant but no longer accurate, update it.
- Never leave stale comments that contradict the code.

### Detecting Stale Comments

- Comment describes a past behavior that has changed.
- Comment mentions a TODO or workaround that no longer applies.
- Comment's logic does not match the current implementation.

## Inline Comments (Use Sparingly)

Inline comments within function bodies are rarely needed if:
- Variable names are clear (`const isMaxedFunding` is self-documenting).
- Logic is straightforward (simple loops, arithmetic).
- The code structure matches what a reader expects.

Use inline comments **only** when:
- A non-obvious algorithm or mathematical operation needs explanation.
- There is a performance reason or trade-off worth noting.
- A workaround for a known browser/library quirk is in place.

```ts
// Performance: Batch database reads to reduce query overhead
const gameIds = await db
  .select({ id: games.id })
  .from(games)
  .limit(100);
```

## Summary

- **Comment intent and decisions, not mechanics.**
- **Export TSDoc/JSDoc for `db/` and `src/lib/` functions.**
- **Document Astro component `Props` interfaces.**
- **Keep comments current when code changes.**
- **Delete stale or incorrect comments — don't leave them as warnings.**
