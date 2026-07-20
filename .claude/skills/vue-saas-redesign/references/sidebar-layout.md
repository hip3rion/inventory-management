# Sidebar Layout

Mechanics for converting a horizontal top nav into a vertical left sidebar.

## Contents

- [The shell](#the-shell)
- [Re-homing what the top bar held](#re-homing-what-the-top-bar-held)
- [Sidebar anatomy](#sidebar-anatomy)
- [Active state](#active-state)
- [Toolbars and filter strips](#toolbars-and-filter-strips)
- [Accessibility](#accessibility)
- [Responsive behavior](#responsive-behavior)
- [Common breakages](#common-breakages)

## The shell

A top-nav app usually stacks vertically:

```css
.app { display: flex; flex-direction: column; }
```

The sidebar version is two columns, full height:

```css
.app {
  display: grid;
  grid-template-columns: var(--sidebar-w) 1fr;
  min-height: 100vh;
}
```

Grid over flex here because the column width is the thing you actually want to declare, and `grid-template-columns` says so directly — it also makes the collapsed state a one-property change.

Let the sidebar own its own scroll so a long nav never drags the page:

```css
.sidebar {
  position: sticky;
  top: 0;
  height: 100vh;
  overflow-y: auto;
  background: var(--color-surface);
  border-right: 1px solid var(--color-border);
}
```

**Drop the centering.** Top-nav layouts commonly center content with `max-width: 1600px; margin: 0 auto`. Once a fixed sidebar owns the left edge, centering the remaining column leaves a lopsided gutter. Either let content fill the column with padding, or cap it with `max-width: var(--content-max)` and leave it left-aligned.

## Re-homing what the top bar held

A top bar usually carries three things: a logo, the nav links, and a cluster of controls (language switcher, profile menu, notifications). The links are the easy part. The controls are where this goes wrong — they get dropped, or pasted into the sidebar bottom where they look accidental.

Two workable arrangements:

**Everything in the sidebar** — logo at top, links in the middle, controls pinned to the bottom with `margin-top: auto`. Maximum vertical space for content; suits apps with few controls.

**Sidebar plus a slim topbar** — logo and links in the sidebar; user controls in a short bar across the content column. Costs `--topbar-h` of height but gives controls a natural home and matches what most SaaS products do. Prefer this when there are more than one or two controls, or when they need to stay reachable as the sidebar scrolls.

Pick one, state the trade-off, and account for every occupant of the old bar. Silently dropping the language switcher is a functional regression, not a design simplification.

## Sidebar anatomy

```
┌──────────────────┐
│  logo / product  │  header — brand, sometimes a workspace switcher
├──────────────────┤
│  ▸ nav item      │  primary nav — one entry per route
│  ▸ nav item      │
│  ▸ nav item      │
│                  │
│      (flex gap)  │  margin-top:auto pushes the footer down
├──────────────────┤
│  user / locale   │  footer — only if not using a topbar
└──────────────────┘
```

Nav items become full-width rows. Give them generous vertical padding (`--space-3`), horizontal padding matching the sidebar's inset, and a radius so the hover and active fills read as deliberate shapes rather than edge-to-edge bands.

Group items with a small uppercase label when there are more than about seven — scanning an undifferentiated list of ten gets slow.

## Active state

The horizontal underline that marks the active tab in a top nav does not translate. `.nav-tabs a.active::after` with a bottom border sits under a full-width row and reads as a divider, not a selection.

Vertical nav wants a filled row plus a left accent:

```css
.nav-item {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-3) var(--space-4);
  border-radius: var(--radius);
  color: var(--color-text-muted);
  border-left: 3px solid transparent;
}
.nav-item:hover { background: var(--color-bg); color: var(--color-text); }
.nav-item.active {
  background: color-mix(in srgb, var(--color-primary) 10%, transparent);
  color: var(--color-primary);
  border-left-color: var(--color-primary);
  font-weight: 600;
}
```

Keep the transparent border on the inactive state so activating an item doesn't shift it sideways by 3px.

Drive the active class from the router rather than manual path comparison where possible — `router-link` applies `router-link-active` automatically, and letting it do so removes a class of bugs where the highlight desyncs from the route.

## Toolbars and filter strips

A full-width filter bar under the top nav becomes a toolbar inside the content column. It no longer spans the viewport, so its horizontal padding should match the content's rather than the old page-level inset.

If it was sticky, keep it sticky — but `top` now measures from the content column: `top: 0` without a topbar, `top: var(--topbar-h)` with one.

**Give the topbar a higher `z-index` than any sticky toolbar below it.** This bites specifically when you choose the sidebar-plus-topbar arrangement and the app already has a sticky filter/toolbar strip — a very common pairing. Both are sticky siblings, so if their `z-index` is equal, ties resolve by DOM order and the *later* element wins. The toolbar then paints over the topbar's entire stacking context, and any dropdown opened from the topbar is swallowed — even one with `z-index: 1000`, because that value only competes *inside* its own context. The symptom is a menu that appears to open but can't be clicked, so it reads as a JS bug rather than a CSS one:

```css
.topbar      { position: sticky; top: 0; z-index: 95; }
.filters-bar { position: sticky; top: var(--topbar-h); z-index: 90; }
```

## Accessibility

- Wrap nav links in `<nav>`; if the page has more than one nav landmark, add `aria-label`.
- Mark the current page with `aria-current="page"`. Color alone doesn't convey state to assistive tech, and this is the one attribute most sidebar rewrites forget.
- Keep a visible `:focus-visible` ring on nav items. Dense sidebars are heavily keyboard-navigated.
- If the sidebar collapses, the toggle needs an accessible name and `aria-expanded`.
- Icon-only collapsed items need their labels available — `aria-label` plus a visual tooltip.

## Responsive behavior

Below roughly 1024px a fixed sidebar eats too much width. Two options:

**Icon rail** — collapse to `var(--sidebar-w-collapsed)`, icons only. Nav stays visible; needs an icon per item, so it's only available if the design has them.

**Off-canvas drawer** — slide the sidebar over the content behind a toggle. Works without icons and is the safer default when nav items are text-only.

```css
@media (max-width: 1024px) {
  .app { grid-template-columns: 1fr; }
  .sidebar {
    position: fixed; inset: 0 auto 0 0;
    transform: translateX(-100%);
    transition: transform 0.2s ease;
    z-index: 50;
  }
  .sidebar.open { transform: translateX(0); }
}
```

Add a scrim behind the open drawer and close it on route change — a drawer that stays open over the page it just navigated to feels broken.

## Common breakages

- **A route loses its nav entry.** Check the rewritten nav against the router's route table; count them.
- **Translated labels become English literals.** Preserve `t('...')` calls exactly.
- **Sticky offsets go stale.** Anything positioned relative to the old 70px top bar needs new coordinates.
- **`z-index` collisions.** Modals and dropdowns were layered against a top bar; a fixed sidebar and drawer scrim introduce new stacking neighbors.
- **Double scrollbars.** Usually `height: 100vh` on both the shell and an inner container. Let one element own the scroll.
- **Auto-fit grids orphan a card.** The content column is now narrower by the sidebar's width, so a `repeat(auto-fit, minmax(280px, 1fr))` grid tuned against the full viewport may fit one fewer column and leave a lone item stranded on its own row. Check the dense grids (KPI tiles, stat cards) after the shell change and retune the `minmax` floor. Nothing is broken, so this is easy to miss — it just looks unconsidered.
- **Content shifts on hover.** A hover state that adds a border or changes font weight without reserving the space nudges the row; use a transparent border or fixed weight.
