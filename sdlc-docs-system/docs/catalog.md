# Documentation Catalog

Read this file first, every time, before opening anything else under `/docs`.
Resolve the exact row(s) you need by `id`, `type`, or `tags`, then open
**only** those file paths. Never open a doc that isn't listed here except
`detailed-design.md` / `screen-elements.md` files, which are intentionally
one hop deeper — reach them via the `detailed_design` link inside the
matching `program-summary.md` row below.

| ID | Type | Path | Status | Tags | One-line |
|----|------|------|--------|------|----------|
| GLOSSARY | global-context | 00-global-context/glossary.md | approved | glossary | Universal domain terminology |
| SYSTEM-CONTEXT | global-context | 00-global-context/system-context.md | approved | c4, actors | C4 Level 1 system actors & external dependencies |
| CAP-INDEX | global-context | 00-global-context/business-capabilities.md | approved | index | Master index of [CAP-XXX] capability ids |
| COMPONENT-HIERARCHY | component-hierarchy | 02-architecture/component-hierarchy.md | approved | hierarchy, master-index | Capability → component → sub-component mapping |

<!--
Add one row per document as it's created by Docs - Author. Keep this file
flat and terse — no multi-line descriptions, no bodies. If a row's Status
is "needs-review", a related doc changed and this one hasn't been
reconciled yet; resolve before treating it as current.
-->
