---
applyTo: ["Docs - Author", "Docs - Sync"]
---

# Documentation Traceability Rules

These rules are shared by `Docs - Author` and `Docs - Sync`. Every other
phase agent that touches `/docs` must call one of these two agents rather
than editing `/docs` directly.

## Context discipline (non-negotiable)
1. Always read `docs/catalog.md` first. Never read any other file under
   `/docs` without first resolving its exact path from the catalog (or from
   a `related`/`detailed_design` link inside a doc you already opened for
   this task).
2. Never open every file in a folder "to be safe." If the catalog doesn't
   have what you need, that's a signal the doc doesn't exist yet — hand off
   to `Docs - Author` to create it, don't go searching the tree.
3. `detailed-design.md` and `screen-elements.md` files are never in
   `catalog.md` by design. Only open them when the task is at
   implementation depth (a Developer phase), via the `detailed_design` link
   in the relevant `program-summary.md`.

## Reciprocity rule
If Doc A's frontmatter lists Doc B's id under any `related.*` field, Doc B
must list Doc A's id under the semantically inverse field:

| A's field | B's reciprocal field |
|---|---|
| `parent` | `children` |
| `children` | `parent` |
| `implements` (technical → capability) | `realized_by` (capability → technical) |
| `consumes` | `consumed_by` / `produced_by` |
| `produces` | `consumed_by` / `produced_by` |
| `data_models` | `owned_by` / `read_by` (on the data model doc) |
| `see_also` | `see_also` (symmetric) |

A doc is never edited to add a forward link without also queuing the
reciprocal edit on the other doc. That queuing/execution is `Docs - Sync`'s
job.

## One-hop propagation (bounded, not recursive)
When a doc changes:
1. Update that doc's own `last_updated` and, if the change is substantive
   (not just a reciprocal link add), its `status`.
2. For every id in its `related` block, open **only that one** related doc
   and reconcile the reciprocal field per the table above.
3. If reconciling a related doc requires more than adding/updating a link
   (i.e. the related doc's own content is now stale), set that doc's
   `status: needs-review` and stop — do not cascade into *its* related
   docs automatically. A human or the relevant phase agent resolves
   `needs-review` docs explicitly on their own next pass.
4. Update `catalog.md` rows for every file touched in steps 1–3 (path,
   status, tags, last_updated). New docs get a new row; nothing is ever
   removed from `catalog.md` without an explicit deprecation.

## Deprecation, not deletion
A doc is never silently deleted. Set `status: deprecated`, add a one-line
reason under a `## Deprecated` heading, and leave the catalog row (marked
deprecated) so historical lineage stays intact for anything that still
references it.

## Failure mode to avoid
The whole point of one-hop propagation is that a single edit's cost is
bounded and predictable — at most (1 changed doc + N directly related docs)
get opened, never the whole tree. If a task seems to require touching more
than that, it is not a documentation-sync task anymore — it's a redesign,
and should go back to a human or to `Architect - Design`.
