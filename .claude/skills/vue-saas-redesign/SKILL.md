---
name: vue-saas-redesign
description: Redesign a Vue 3 application's UI into a modern SaaS-style interface — convert a horizontal top nav into a vertical left sidebar, introduce a consistent design-token spacing scale, and polish the overall look. Use this skill whenever someone wants a Vue app to look more modern, professional, polished, or "like a real SaaS product"; whenever they mention a sidebar, side nav, left nav, vertical navigation, or replacing/removing a top nav bar; whenever they complain that spacing, padding, or margins look inconsistent or cramped in a frontend; and whenever they ask for design tokens, a spacing scale, or a design system in a Vue codebase. This applies to any Vue 3 app, not one specific project — reach for it even when the word "redesign" never appears.
---

# Vue 3 SaaS Redesign

Convert a Vue 3 app from a top-nav layout into a sidebar-driven SaaS interface, and put its spacing on a single scale.

Two things make this task fail in practice, and the workflow below is shaped around avoiding them:

- **Restyling silently breaks behavior.** Nav links carry router bindings and often i18n lookups. A rewritten shell that drops one route, hardcodes a label that used to be translated, or detaches a modal trigger is a regression wearing a redesign's clothes.
- **"It looks broken now" gets misattributed.** Most real apps already have console noise and half-finished features before you arrive. Without a record of the starting state, every pre-existing error becomes your fault and you burn time chasing it.

Work in two phases with a checkpoint between them. The shell change is dramatic and worth a human glance before you touch fifteen more files.

## Step 1 — Map the app

Every Vue app arranges these differently, so find them rather than assuming:

- **The shell component** — usually `src/App.vue`. It holds the nav markup and often a non-scoped `<style>` block acting as the de facto global stylesheet.
- **The route table** — `src/main.js`, `src/router/index.js`, or similar. This is the source of truth for which nav links must exist. Enumerate the routes; the sidebar has to end up with the same set.
- **The style layer** — is there a `:root` block with custom properties already? A `styles/` directory? Tailwind? Or is everything hardcoded across `<style scoped>` blocks? This determines whether you are creating a token layer or extending one.
- **Label indirection** — check whether nav text is literal (`Orders`) or a lookup (`{{ t('nav.orders') }}`). Preserving i18n keys matters; replacing them with English strings quietly breaks every other locale.
- **A project Vue specialist** — see [Delegation](#delegation) below.

Report what you found before editing. If the app already has a sidebar, or the "top nav" turns out to be something else, say so and agree on the goal rather than proceeding on a wrong premise.

## Step 2 — Baseline the current state

Before changing anything, capture what the app looks like and what it already complains about:

1. Make sure the app runs (start the dev server, and the API if the UI depends on one).
2. Screenshot every route with Playwright.
3. Record the console errors and warnings on load.

Keep that error list. At the end you compare against it, so the question becomes "did I add anything new?" rather than "is this app healthy?" — a much easier question to answer honestly, and the only one you're responsible for.

## Step 3 — Establish the token layer (Phase 1)

Read `references/design-tokens.md` for the scale, naming, and the table for snapping off-scale values.

The key judgment: **derive tokens from the palette the app already uses**, rather than importing a fresh one. The existing colors are load-bearing — status badges, chart series, and hover states depend on specific hues, and swapping them wholesale turns a spacing cleanup into an unrequested rebrand. Sample the real values, group them, and name them. Where the app has near-duplicates (three greys a few percent apart), collapse them; that consolidation *is* the polish.

Define tokens once in the global stylesheet as `:root` custom properties. Don't convert any component styles yet.

## Step 4 — Convert the shell to a sidebar (Phase 1)

Read `references/sidebar-layout.md` for the layout mechanics, active-state patterns, accessibility, and responsive behavior.

The part most often botched is **re-homing what the top bar was holding**. A top nav typically carries a logo, the links, and a cluster of controls (language switcher, profile menu, notifications). A vertical sidebar has different affordances: it has room for a logo and links, but a stack of user controls at the bottom of a tall column reads oddly unless deliberately designed. Decide where each occupant goes and say why — don't let one silently disappear.

Everything the shell rendered before must still render and still work: same routes, same components, same props and events.

**Shared components positioned against the old shell need fixing now, not in Phase 2.** A sticky toolbar or filter bar isn't the shell, but it was measured against the shell — its `top` offset, its centering, its `z-index` neighbors. Leaving those for the sweep means shipping a visibly broken checkpoint. Repair only what the layout change broke and leave the component's other values alone; its full token conversion is still Phase 2 work.

## Checkpoint

Stop here. Re-screenshot the routes, confirm navigation works and no new console errors appeared, and show the user before starting the sweep. The shell is the change they most want a say in, and finding out now that the sidebar should be narrower is far cheaper than after fifteen files have moved.

## Step 5 — Sweep views onto the scale (Phase 2)

Go file by file through the component stylesheets, replacing literal values with tokens.

Snap off-scale values to the nearest step instead of preserving them. A `0.938rem` padding exists because someone nudged it once, not because the design needs that exact value; keeping it defeats the point. The visual difference at these magnitudes is imperceptible, while the consistency gain is the whole objective.

Two things deserve care rather than mechanical replacement:

- **Intent differs from value.** Two components using `1rem` may mean "gap between sibling cards" and "inset padding". Map each to the token that matches its role, so future changes to one don't drag the other along.
- **Some values aren't spacing.** Border widths, icon dimensions, and chart geometry happen to be small numbers too. Leave them alone.

Work through the files in batches and keep the app running so you can catch a layout break as it happens rather than at the end.

## Step 6 — Verify

Re-screenshot every route and compare against the baseline from Step 2. Confirm:

- Every route from the router still has a working nav entry, and the active state tracks the current route.
- Interactive shell pieces still function — filters still drive data, language switching still changes copy, menus and modals still open.
- Console errors match the baseline set. Anything new is yours to fix.
- Spacing literals actually collapsed onto tokens. Re-run the frequency scan from Step 1 and compare; if a dozen distinct `gap` values remain, the sweep didn't land.

Show before/after screenshots side by side. This is design work, and the user's eye is the real acceptance test — a passing checklist over an ugly result is not success.

## Delegation

If the project defines a Vue specialist agent (check `.claude/agents/` and the project's `CLAUDE.md`), route the `.vue` edits through it. Project agents encode local conventions — component structure, Composition API style, which directories are off-limits — and some projects require this. Give the agent the token definitions and the target layout so it isn't reinventing decisions you already made.

Keep the redesign on its own branch. It touches many files and produces a large diff, and being able to abandon it cleanly is worth the one command it costs.

## Reference files

- `references/design-tokens.md` — spacing scale, color/radius/shadow tokens, naming, and the off-scale snapping table. Read before Step 3.
- `references/sidebar-layout.md` — shell grid, sidebar anatomy, active states, re-homing top-nav controls, accessibility, responsive collapse. Read before Step 4.
