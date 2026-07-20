# Design Tokens

How to build the token layer that the rest of the redesign leans on.

## Contents

- [Why tokens first](#why-tokens-first)
- [Spacing scale](#spacing-scale)
- [Snapping off-scale values](#snapping-off-scale-values)
- [Deriving color tokens](#deriving-color-tokens)
- [Radius, shadow, typography](#radius-shadow-typography)
- [Layout tokens](#layout-tokens)
- [Where to define them](#where-to-define-them)

## Why tokens first

"Inconsistent spacing" is rarely about any single wrong value — it's that nothing constrains the choices, so each component invents its own. Defining the scale before touching components means the sweep becomes a lookup instead of a series of fresh judgment calls, and the result is consistent because it *can't* drift, not because someone was careful.

Do this before the shell conversion so the new sidebar is built from tokens rather than retrofitted later.

## Spacing scale

A 4px base step is the common choice: fine enough for tight UI, coarse enough to prevent drift. At a 16px root, `0.25rem` = 4px.

```css
--space-1:  0.25rem;  /*  4px */
--space-2:  0.5rem;   /*  8px */
--space-3:  0.75rem;  /* 12px */
--space-4:  1rem;     /* 16px */
--space-5:  1.25rem;  /* 20px */
--space-6:  1.5rem;   /* 24px */
--space-8:  2rem;     /* 32px */
--space-10: 2.5rem;   /* 40px */
--space-12: 3rem;     /* 48px */
```

Numeric names (`--space-4`) travel better than semantic ones (`--space-md`). Semantic names run out fast — once you need something between `md` and `lg` the scheme collapses, whereas a numeric scale always has room and the ordering is self-evident.

Skipping 7, 9, 11 is deliberate: the gaps discourage reaching for a bespoke step when an existing one is close enough.

## Snapping off-scale values

Most codebases accumulate values that came from nudging rather than design. Round to the nearest step:

| Found | Snap to | px |
|---|---|---|
| `0.313rem` (5px) | `--space-1` | 4 |
| `0.375rem` (6px) | `--space-2` | 8 |
| `6px` | `--space-2` | 8 |
| `0.625rem` (10px) | `--space-3` | 12 |
| `0.875rem` (14px) | `--space-3` or `--space-4` | 12 / 16 |
| `0.938rem` (15px) | `--space-4` | 16 |
| `1.75rem` (28px) | `--space-6` or `--space-8` | 24 / 32 |

Where two steps are equally close, pick by role: tighten insets within a component, and favor the larger step for separation between components — generous separation is most of what reads as "polished".

A caveat worth respecting: `0.875rem` and `0.938rem` are frequently *font sizes*, not spacing. Those belong to the type scale and shouldn't be swept into spacing tokens.

## Deriving color tokens

Sample the palette already in the codebase rather than importing a new one. Existing colors carry meaning — status badges, chart series, hover states — and replacing them turns a spacing cleanup into an unrequested rebrand.

Collect every color literal, group near-duplicates, and name by **role**, not appearance. `--color-border` survives a theme change; `--color-grey-200` becomes a lie the moment the theme shifts.

A typical grouping:

```css
/* surfaces */
--color-bg: #f8fafc;          /* app background */
--color-surface: #ffffff;     /* cards, sidebar, menus */
--color-border: #e2e8f0;

/* text */
--color-text: #0f172a;        /* primary */
--color-text-muted: #64748b;  /* labels, secondary */

/* accent */
--color-primary: #3b82f6;
--color-primary-contrast: #ffffff;

/* status — keep whatever the app already uses */
--color-success: #16a34a;
--color-warning: #d97706;
--color-danger:  #dc2626;
--color-info:    #3b82f6;
```

Consolidating near-duplicates is where much of the polish comes from. Three greys within a few percent of each other read as sloppiness even when no one can name why; collapsing them to one is invisible individually and obvious in aggregate.

Preserve status hues exactly. Badge and chart classes depend on them, and shifting them changes meaning rather than appearance.

## Radius, shadow, typography

Small scales, same reasoning:

```css
--radius-sm: 4px;
--radius:    8px;
--radius-lg: 12px;
--radius-full: 9999px;

--shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.06);
--shadow:    0 1px 3px rgba(15, 23, 42, 0.08);
--shadow-lg: 0 8px 24px rgba(15, 23, 42, 0.10);
```

Tinting shadows toward the text color instead of pure black keeps them from looking muddy over colored surfaces.

For type, capture what exists as a scale rather than inventing one, and keep the count low — a handful of sizes with clear jumps looks more deliberate than a dozen with 1px differences.

## Layout tokens

The shell needs a few of its own. Defining the sidebar width as a token matters more than it looks: both the sidebar and the content column reference it, and hardcoding it in two places guarantees they drift.

```css
--sidebar-w: 260px;
--sidebar-w-collapsed: 72px;  /* only if collapsing to an icon rail */
--topbar-h: 56px;             /* only if using a topbar */
--content-max: 1440px;
```

The last three are conditional on choices made during the shell conversion: `--sidebar-w-collapsed` matters only for the icon-rail responsive strategy (an off-canvas drawer never renders a collapsed width), and `--topbar-h` only if user controls land in a topbar rather than the sidebar footer. Define what the chosen layout actually uses — an unused token is dead CSS that implies a feature the app doesn't have.

## Where to define them

Put them in a `:root` block in the global (non-scoped) stylesheet — commonly the `<style>` block in `App.vue`, or a dedicated CSS file imported by the entry point. Custom properties cascade, so `:root` makes them reachable from every `<style scoped>` block without imports.

Define them once. Tokens redefined in a second place stop being tokens.
