# Code reuse patterns

Concrete things to look for during the cross-file pass. Unlike performance findings, most of these only become visible by holding a pattern in mind while reading several files — see Step 2 of SKILL.md.

## Duplicated fetch/loading/error scaffolding

The single most common Vue duplication: every view that loads its own data re-implements the same shape — a `loading` ref, an `error` ref, a data ref, a function that sets `loading = true`, calls the API, assigns the result or the error, sets `loading = false`. Three or more views with this shape is a `useAsyncData`/`useFetch`-style composable, not a coincidence.

```js
// Flag: this shape, repeated across Orders.vue, Inventory.vue, Spending.vue
const loading = ref(true)
const error = ref(null)
const items = ref([])
async function load() {
  loading.value = true
  error.value = null
  try {
    items.value = await api.getOrders(filters.value)
  } catch (e) {
    error.value = e.message
  } finally {
    loading.value = false
  }
}
onMounted(load)
watch(filters, load)

// Fix: one composable, parameterized by the fetch function
function useAsyncData(fetchFn, source) {
  const loading = ref(true)
  const error = ref(null)
  const data = ref(null)
  async function load() {
    loading.value = true
    error.value = null
    try {
      data.value = await fetchFn()
    } catch (e) {
      error.value = e.message
    } finally {
      loading.value = false
    }
  }
  onMounted(load)
  if (source) watch(source, load)
  return { loading, error, data, reload: load }
}
```

Name the finding by *how many* places repeat it and list them all — the fix's value is proportional to the count, so the report should make the count visible, not just assert "this is duplicated."

## Duplicated modal/panel shell

Detail modals, confirmation dialogs, and side panels tend to each reimplement the same chrome — backdrop, close button, header/title slot, transition — with the actual content as the only real difference. Look for repeated backdrop/overlay markup, near-identical `@click.self="close"` + Escape-key handling, and repeated transition wrapper markup across files named like `*Modal.vue` or `*Dialog.vue`.

```html
<!-- Flag: this shell repeated in every *Modal.vue -->
<Teleport to="body">
  <div v-if="isOpen" class="modal-overlay" @click.self="close">
    <div class="modal-panel">
      <button class="modal-close" @click="close">×</button>
      <h2>{{ title }}</h2>
      <!-- content differs -->
    </div>
  </div>
</Teleport>
```

**Fix:** a `BaseModal.vue` wrapping the shell, taking `isOpen`/`title` as props and exposing a default slot (plus a `header`/`footer` slot if headers vary) for the content each caller currently hand-rolls. Existing modals become thin wrappers around it.

**Not a finding:** two modals sharing only trivial markup (a `<div class="overlay">`) with materially different structure otherwise — extracting a wrapper for two lines saved isn't worth the indirection. Look for three-plus occurrences or a shell substantial enough (10+ lines) that keeping it in sync manually is a real risk.

## Prop drilling

A prop passed through an intermediate component only to be forwarded, unused by that component itself, is drilling. One layer of forwarding is often fine and cheap to trace; two or more layers, or the same prop forwarded through several different component chains, is worth flagging — the fix is usually `provide`/`inject` for cross-cutting concerns (theme, current user, active filters) or lifting the state into a composable both ends can call directly, rather than a prop chain.

```html
<!-- Flag: Dashboard -> SummaryPanel -> SummaryCard, `currency` untouched at each hop -->
<!-- Dashboard.vue -->
<SummaryPanel :currency="currentCurrency" />
<!-- SummaryPanel.vue -->
<SummaryCard :currency="currency" />
<!-- SummaryCard.vue actually uses `currency` -->
```

**Fix:** if `currentCurrency` comes from a composable (`useI18n`, `useSettings`), have `SummaryCard` call that composable directly instead of receiving it as a prop — cutting the two intermediate hops. Reach for `provide`/`inject` when the value doesn't already live in an importable composable/store and many descendants at varying depths need it.

**Not a finding:** a prop passed one level down, or passed through a component that also uses it itself (that's normal composition, not drilling) — and props like `class`/`id` that are conventionally forwarded.

## Oversized "god components"

A single-file component mixing data fetching, business-rule computation, and presentation for multiple unrelated sections is hard to test, hard to reuse, and risky to touch (a change meant for one section can affect another via shared local state). Line count is a proxy, not the metric itself — a 400-line component that's one cohesive table with many columns is fine; a 400-line component juggling five unrelated widgets (KPI cards + a chart + a shortage table + a top-products table, each with its own fetch/filter/format logic) is the real target.

When flagging one, name the seams: which chunks of `setup()` and template are independent enough to become their own component or composable. That's more actionable than "this file is too long."

```
// Example seam analysis for a 1200-line Dashboard.vue mixing 5 concerns:
// - KPI card computation + markup -> KpiGrid.vue (props: metrics)
// - inventory-value-by-category chart -> InventoryValueChart.vue
// - shortage table + its own sort/filter state -> ShortageTable.vue
// - top-products table -> TopProductsTable.vue
// - the fetch-all-dashboard-data + loading/error handling -> useDashboardData() composable
// Dashboard.vue becomes: call the composable, pass slices of the result to each child.
```

## Duplicated business logic, not just markup

The subtler version of duplication: the same *calculation* (a currency formatter, a status-to-color mapping, a date-range filter, a "is this item low stock" threshold check) reimplemented slightly differently in multiple components instead of imported from one place. This one is riskier than markup duplication because the copies tend to drift — one gets a bugfix, the others don't.

```js
// Flag: this threshold logic, slightly different, in InventoryDetailModal.vue and Inventory.vue
const isLowStock = item.quantityOnHand < item.reorderPoint * 1.1  // one file
const isLowStock = item.quantityOnHand <= item.reorderPoint       // another file, different logic!
```

This is worth flagging even at two occurrences, unlike markup duplication, because divergent business logic is a correctness bug waiting to happen, not just a style issue — say so explicitly in the report rather than filing it as a routine reuse suggestion.

## What's usually not worth flagging

- Two components that happen to both have a `loading` ref for unrelated reasons — pattern-match on *shape and purpose together*, not on variable names.
- A prop forwarded exactly once, or forwarded through a component that also consumes it.
- Short, coincidentally-similar markup (a `<div class="card">` wrapper) that would cost more in indirection to extract than it saves.
- Components already large but genuinely single-purpose (a big form with many fields, a big table with many columns) — size alone isn't the finding; mixed, separable concerns are.
