---
name: vue-component-audit
description: Analyze Vue 3 component structure and produce a findings report on performance and code-reuse opportunities — unnecessary reactivity, missing v-for keys, prop drilling, duplicated markup/logic that should become a shared component or composable, oversized "god components," and missed memoization. Use this whenever someone asks to audit, review, or optimize Vue components or a Vue app's frontend; whenever they say a component is "too big," "slow," "re-rendering too much," or "hard to work with"; whenever they ask where to extract a composable or shared component; or whenever they mention code duplication, prop drilling, or DRY-ing up a Vue codebase — even if they don't name a specific file. Produces a report with concrete file:line references and suggested fixes; does not modify code unless explicitly asked to apply a fix afterward.
---

# Vue 3 Component Audit

Read a Vue 3 Composition API codebase (or a subset the user points at) and produce a findings report on two axes — performance and code reuse — grounded in specific files and lines, not general advice.

This is a **report-writing skill, not a refactoring skill.** Finding real, specific, file-anchored issues and explaining them well is the job. Only edit code if the user asks you to apply a fix after seeing the report — see [Applying fixes](#applying-fixes).

## Why this needs a workflow, not just "review the code"

Two things make an unstructured review go wrong:

- **Over-flagging drowns the real findings.** Every Vue app has *some* inline function, *some* two-line duplication, *some* computed that recalculates a cheap expression. Flagging all of it produces a report nobody reads past the first ten items, and trains the user to skim past your next one too. The bar for "worth reporting" is real cost — measurable re-render/recompute overhead, or duplication with a genuine shared-behavior risk (fix it in one copy, forget the other three) — not stylistic preference.
- **Duplication needs cross-file reading, not per-file review.** A component reviewed in isolation never reveals that four other views wrote the same 15-line fetch-loading-error pattern. The reuse half of this audit only works if you deliberately compare files against each other, which is Step 2 below, not a side effect of Step 1.

## Step 1 — Map the scope

Don't assume the layout; find it:

- **What's in scope.** Did the user name specific files, a directory, or "the app"? Default to the whole component tree (views + components + composables) if unstated, but say what you're covering before diving in — a partial scope silently presented as complete is misleading.
- **The composable and store layer.** Look for a `composables/`, `stores/`, or `hooks/` directory. Its existence changes what counts as a finding: a codebase with an established `useFetch`-style composable pattern makes copy-pasted fetch logic elsewhere a clearer miss than in a codebase with no such convention yet.
- **Component sizes.** A quick line count across the component tree (`wc -l` on `.vue` files, sorted) tells you where to look first — the largest files are the most likely "god components" and the best place to start reading closely.
- **Any existing performance/architecture notes.** Check `CLAUDE.md` or similar for stated conventions (e.g. "raw data in refs, derived data in computed") — a finding that contradicts a documented convention is stronger than one that's just your opinion.

## Step 2 — Scan by category

Read `references/performance-patterns.md` and `references/reuse-patterns.md` before scanning — they give the concrete before/after patterns to look for in each category, worked examples, and the judgment calls (what's a real finding vs. noise) that keep this audit useful rather than pedantic.

Work in two passes because they need different reading strategies:

1. **Per-file pass (performance).** Go file by file, largest first. For each component, read the `<script setup>`/`setup()` block for reactive-value definitions and the `<template>` for how they're consumed. Most performance findings are local to one file.
2. **Cross-file pass (reuse).** This is comparative, not sequential — hold candidate patterns in mind (a modal shell, a fetch-load-error block, a filter-bar wiring pattern) and grep/read across files to see how many times each recurs before writing it up. Two occurrences is often coincidence; three or more with matching structure is a pattern worth naming.

## Step 3 — Write the report

Use this structure. Skip a section entirely if it has no findings — don't pad with "no issues found" filler.

```markdown
# Vue Component Audit — <scope>

<one or two sentences: what was covered, and the headline signal (e.g. "3 components exceed 500 lines">

## Performance

### <Title> — `path/to/File.vue:L123`
**Impact:** High | Medium | Low
**What:** <the specific code pattern, quoted or closely paraphrased>
**Why it matters:** <the concrete cost — what re-renders/recomputes unnecessarily, and roughly how often>
**Fix:** <the specific change, with a short before/after snippet>

## Code Reuse

### <Title> — `path/to/A.vue:L45`, `path/to/B.vue:L88`, `path/to/C.vue:L20`
**Impact:** High | Medium | Low
**What:** <the duplicated shape, named — e.g. "identical fetch/loading/error scaffold">
**Why it matters:** <the shared-maintenance risk — what breaks if one copy is fixed and the others aren't>
**Fix:** <the extraction target — composable name and signature, or shared component name and props/slots>

## Considered, not flagged
<optional — near-misses you weighed and decided weren't worth the churn, so the user can calibrate the bar with you>
```

Order findings within each section by impact, highest first. A report where the reader has to hunt for the important item is worse than a shorter one.

## Applying fixes

If the user asks you to act on a finding (or all of them), treat each as its own small change: extract the one composable, split the one component, fix the one `v-for` key — verify it still works (start the dev server, exercise the affected view) before moving to the next, rather than batching every finding into one large, hard-to-review diff. If this project defines a Vue specialist agent (check `.claude/agents/` and `CLAUDE.md`), route the actual `.vue` edits through it — it encodes local conventions this audit doesn't need to know about to *find* the issue, but that matter when *fixing* it.

## Reference files

- `references/performance-patterns.md` — reactivity misuse, `v-for` keys, watcher vs. computed, memoization opportunities, with before/after examples and what's *not* worth flagging.
- `references/reuse-patterns.md` — duplicated markup/logic, prop drilling, oversized components, composable/component extraction, with the same before/after treatment.
