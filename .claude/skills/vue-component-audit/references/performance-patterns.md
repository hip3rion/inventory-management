# Performance patterns

Concrete things to look for during the per-file pass, roughly in the order they're worth checking. Each entry has what a real finding looks like and what a non-finding looks like, because the calibration matters as much as the pattern.

## Watcher doing a computed's job

A `watch`/`watchEffect` that exists purely to derive one reactive value from another — no side effect beyond `someRef.value = ...` — is a computed property with extra machinery. It re-runs on the same triggers a computed would, but loses memoization (a computed caches until its dependencies change; a watcher's assignment re-runs and re-triggers downstream reactivity every time, even if the derived value is identical) and adds a lifecycle to reason about.

```js
// Flag: watcher used as a pure derivation
const filteredItems = ref([])
watch([items, filterText], () => {
  filteredItems.value = items.value.filter(i => i.name.includes(filterText.value))
})

// Fix: computed
const filteredItems = computed(() =>
  items.value.filter(i => i.name.includes(filterText.value))
)
```

**Not a finding:** a watcher that does something a computed can't — calling an API, writing to `localStorage`, imperatively touching the DOM, or intentionally running only on specific transitions (`{ flush: 'post' }`, ignoring the initial run). Those are watchers because they need to be.

## `v-for` without a stable key, or keyed on array index

A missing key falls back to index-based patching; an *explicit* index key has the same problem while looking deliberate. Either way, inserting, removing, or reordering the list causes Vue to patch the wrong DOM nodes against the wrong data — visible as stale form inputs, wrong items highlighted, or transition animations firing on the wrong element. Static, append-only lists that never reorder or filter are the one case where index keys are harmless, but that's rarely true of anything backed by API data or a filter/sort UI.

```html
<!-- Flag: index key on a filterable/sortable list -->
<tr v-for="(order, index) in filteredOrders" :key="index">

<!-- Fix: a real identity -->
<tr v-for="order in filteredOrders" :key="order.id">
```

Check the data has a stable unique field before suggesting one — if it genuinely doesn't, that's its own finding (upstream data shape), not something to paper over with a composite string key unless the fields used are actually stable together.

## Reactivity wrapping data that's never mutated in place

`ref`/`reactive` cost is proportional to what Vue has to instrument and track, not to array/object size directly — but a large deeply-reactive object where only the top-level reference ever changes (chart config passed to a third-party lib, a big static lookup table, a large payload that's replaced wholesale on each fetch rather than mutated field-by-field) is paying for deep reactivity tracking it never uses.

```js
// Flag: deep-reactive payload that's always replaced, never patched in place
const dashboardData = reactive({ orders: [], inventory: [], summary: {} })
async function load() {
  const res = await api.getDashboard()
  dashboardData.orders = res.orders       // triggers deep reactivity machinery
  dashboardData.inventory = res.inventory
}

// Fix: shallowRef when the whole thing is swapped, not patched
const dashboardData = shallowRef({ orders: [], inventory: [], summary: {} })
async function load() {
  dashboardData.value = await api.getDashboard()   // one shallow trigger
}
```

Also applies to non-reactive-by-nature values accidentally made reactive: a third-party class instance (a chart, a map, a form-library controller) stored in a plain `ref`/`reactive` gets proxied and its internals tracked for no benefit — wrap it in `markRaw()`.

**Not a finding:** small objects, or objects whose nested fields genuinely drive independent template bindings (form state where each field's own reactivity matters).

## Expensive computed re-running on a broader dependency set than it needs

A computed that touches five reactive sources but only conceptually depends on two will re-run on changes to the other three too. Common in components with one large `computed` reading from a shared `props`/`state` object where only a couple of fields are actually used — narrowing what's destructured/read limits the dependency set.

```js
// Flag: reads the whole filters object, recomputes on any filter change
const summaryCards = computed(() => {
  return buildCards(orders.value, filters.value) // uses filters.category only
})

// Fix: depend on what's actually used
const summaryCards = computed(() => {
  return buildCards(orders.value, filters.value.category)
})
```

This is a smaller-impact finding than the others — worth noting when the computed body is doing real work (array transforms over hundreds+ of items), not worth it for cheap computations where the extra recompute is invisible.

## `v-if` thrashing where `v-show` fits

`v-if` destroys and recreates the element (and everything under it — child component `setup()` re-runs, local state resets); `v-show` just toggles CSS. A conditional that flips frequently in response to user interaction (tab switching, hover-revealed panels, a toggle the user clicks repeatedly) pays teardown/rebuild cost on every flip. A conditional that's set once and rarely changes (auth-gated sections, feature flags, initial-load skeletons) is fine as `v-if` — that's the more common and more correct choice, so don't flag it reflexively.

## Missing code-splitting on rarely-shown heavy components

A large modal, chart, or editor component that's imported and registered normally gets bundled into the initial chunk even though it only renders after a user action. `defineAsyncComponent` (or route-level lazy `import()`) defers that cost to when it's actually needed. Worth flagging when the component is large (a few hundred+ lines, or pulls in a heavy dependency) and gated behind an explicit user action (opening a modal, expanding a rarely-used panel) — not worth it for small components or ones visible on initial load anyway.

## What's usually not worth flagging

- Inline arrow functions as event handlers (`@click="() => doThing(id)"`) — a real React-style re-render concern doesn't map directly onto Vue 3's reactivity model; the cost is a new function allocation per render, immaterial outside extremely hot lists.
- A computed with a cheap one-line body "recomputing more than needed" — the recompute costs nanoseconds; flagging it is noise.
- `deep: true` watchers on genuinely small objects (a handful of fields) — the deep-comparison cost only matters at scale.
