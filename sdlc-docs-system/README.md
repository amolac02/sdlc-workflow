# Federated Documentation System — Design Reference

This defines how `/docs` is structured so agents can **search → resolve → load
only what they need**, and how documentation stays traceable (business →
technical, and technical ↔ technical) whenever anything changes.

It has three parts:
1. **Document structure** — a universal frontmatter schema every doc uses, plus a body structure per doc type.
2. **catalog.md** — the single always-loaded index that makes documents discoverable without opening them.
3. **Documentation agents** — two new agents (`Docs - Author`, `Docs - Sync`) that create/update docs and enforce traceability.

---

## 1. Why frontmatter, not just folders

Folder location tells you *what kind* of thing a doc is. It doesn't tell an
agent *which other docs* it needs to open next, or let it search by
capability/component ID across the whole tree without opening every file.
Frontmatter fixes that: every doc carries structured, machine-readable
metadata that both `catalog.md` and other docs' `related` links point at.

### Universal frontmatter (every document, no exceptions)

```yaml
---
id: CAP-RPT-STM                # unique, stable, matches folder/file naming
type: capability-l2            # see type vocabulary below
title: Statements Sub-Capability
status: approved                # draft | approved | needs-review | deprecated
owner: PO                       # role that owns sign-off for this doc
last_updated: 2026-07-26
tags: [statements, wealth-reporting, pdf-generation]
related:
  parent: [CAP-RPT]
  children: [CAP-RPT-STM-PDF]
  implements: []                 # technical docs only: which CAP-XXX this realizes
  realized_by: [SVC-trade-validator, BATCH-eod-aggregator]  # capability docs only: which technical components implement this
  consumes: []                   # external interfaces / services this depends on
  produced_by: []                # inbound feeds that populate this
  produces: []                   # what this feeds downstream
  data_models: []                # DATA-* ids referenced
  see_also: []                   # loose cross-references, no strict semantics
---
```

**Type vocabulary** (matches the folder taxonomy 1:1 so `type` can be derived
from path, but is stored explicitly so search doesn't require a path parse):
`capability-l1`, `capability-l2`, `architecture-pattern`, `component-hierarchy`,
`external-interface`, `data-model`, `data-lineage`, `ui-component`,
`service-component`, `batch-component`, `global-context`.

**ID convention** (stable, referenced everywhere instead of file paths):
- Business capability: `CAP-<L1>` / `CAP-<L1>-<L2>` (e.g. `CAP-RPT`, `CAP-RPT-STM`)
- External interface: `EXT-<name>` (e.g. `EXT-market-data-api`)
- Data model: `DATA-<name>` (e.g. `DATA-db2-client-profile`)
- Technical component: `UI-<name>`, `SVC-<name>`, `BATCH-<name>` (e.g. `SVC-trade-validator`)

Only `id` is load-bearing for linking — paths can move, IDs shouldn't.

### Only `related` links matter for traceability

A doc is never "orphaned only in one direction." If Doc A lists `B` under
`related.realized_by`, Doc B must list `A` under `related.implements`. This
reciprocity is what `Docs - Sync` enforces (Part 3) — it's what makes
traceability a checked invariant instead of a hope.

---

## 2. catalog.md — the one file every agent loads first

`catalog.md` is deliberately terse: **one row per document**, no descriptions
beyond a single line, no bodies. Its whole job is to let an agent go from
"I need CAP-RPT-STM's technical flow" or "what covers trade validation" to
**one exact file path** without opening anything else.

```markdown
| ID | Type | Path | Status | Tags | One-line |
|----|------|------|--------|------|----------|
| CAP-RPT | capability-l1 | 01-business-capabilities/CAP-RPT/_description.md | approved | reporting, statements | Reporting capability: client statement & document generation |
| CAP-RPT-STM | capability-l2 | 01-business-capabilities/CAP-RPT/CAP-RPT-STM/_description.md | approved | statements, pdf | Statement generation sub-capability |
| SVC-trade-validator | service-component | 05-technical-components/services/trade-validator/program-summary.md | approved | validation, trades | Validates trade instructions before booking |
| EXT-market-data-api | external-interface | 03-external-interfaces/inbound-sources/market-data-api.md | approved | market-data, inbound | Real-time price feed from vendor X |
```

**Loading protocol for every content-consuming agent:**
1. Read `catalog.md` only (never the full tree).
2. Filter rows by `tags`/`id`/`type` matching the task at hand.
3. Open **only** the exact file paths resolved from step 2.
4. If a doc's `related` block references an ID not needed for the current
   task, do not follow it — that's what keeps context small. Only follow
   `related` links when the task explicitly requires lineage (e.g. impact
   analysis, or the `Docs - Sync` agent).

This mirrors the existing `sdlc-artifacts` single-file-read discipline
already enforced for phase agents.

---

## 3. Document body structures (per type)

Templates for every type live in `docs/_templates/`. Summary of what each
contains beyond the shared frontmatter:

| Type | Body sections |
|---|---|
| `global-context` `business-capabilities.md` | Flat index table: every [CAP-XXX] id, name, one-line, path — entry point, not a restatement of each capability's own description |
| `global-context` `glossary.md` | Term/definition table, business terminology only |
| `global-context` `system-context.md` | Actors, external dependencies (by `EXT-*` id, not restated), system boundary |
| `capability-l1` / `-l2` `_description.md` | Personas, Business Value, Rules, Boundaries (what's out of scope) |
| `capability-l1` / `-l2` `_technical-flow.md` | Narrative flow (online/batch mix), ordered list of technical components involved (as `id` references, not descriptions) |
| `component-hierarchy.md` | Single master table: Capability → Component → Sub-component → `id` → path |
| `architecture-pattern` (`02-architecture/patterns/*`) | Problem, Solution, Where It's Used (by id), Rationale/Trade-offs |
| `external-interfaces/*.md` | Direction, protocol, payload shape (fields + types, not full schema unless small), consumer/producer ids |
| `data-architecture/models/*.md` | Fields, types, keys, owning component id |
| `data-architecture/lineage/*.md` | Source → System → Target rows referencing `DATA-*` and component ids |
| `technical-components/*/program-summary.md` | Purpose, Inputs/Outputs, external calls (ids only), realized capability id(s) — **this is the file catalog.md indexes** |
| `technical-components/*/detailed-design.md` / `screen-elements.md` | Deep internal detail — **not indexed in catalog**, only linked from its own `program-summary.md`, so it's loaded only when an agent is actually implementing/modifying that component |

Note the two-tier depth pattern for technical components: `catalog.md` only
points at `program-summary.md`. `detailed-design.md` is one hop further —
reachable via a `related.see_also` / explicit link inside `program-summary.md`,
never loaded unless the task is at implementation depth. This is the main
lever for keeping context small: browsing/impact-analysis tasks stop at
program-summary; only `Developer - Implement` goes one hop deeper.

---

## 4. Documentation agents

Two new agents, added to the existing 10-agent chain, using the same
`.agent.md` convention as the SDLC phase agents.

### `Docs - Author`
Creates **or** updates documentation from one or more input artifacts
(business requirements, IT specs, story elaborations, Architect design
output, Developer implementation plans, or an existing doc pointed at for
revision). A single run can touch several documents — e.g. a new component
means a new `program-summary.md` *and* an update to its owning capability's
`_technical-flow.md` and to `component-hierarchy.md`.

- **Inputs:** one or more artifacts in a single run — not limited to one
  triggering document.
- **Asks the user for related labels** whenever the input doesn't
  explicitly state which capability/component id(s) a doc relates to. It
  never guesses a `related` link — it surfaces plausible candidates from
  `catalog.md` and has the user confirm before writing anything.
- **Creates or updates**, in the same run, whichever business and/or
  technical documents the input(s) actually affect.
- **Performs its own traceability propagation** — no handoff needed. After
  writing content it reconciles reciprocal `related` links one hop and
  updates every affected row in `catalog.md` itself, in the same turn.

### `Docs - Sync` (standalone fallback)
Not part of `Docs - Author`'s normal flow — `Docs - Author` already
reconciles anything it touches inline. `Docs - Sync` exists for the one
other case: a document changed **outside** of `Docs - Author` (a manual
edit, or a phase agent that edited a doc in place itself) and needs its
links and `catalog.md` reconciled after the fact. Same bounded one-hop
rules apply — it never cascades past the directly related documents, and
flags anything further out as `needs-review` for a human or the relevant
phase agent to resolve explicitly.

This bounded, one-hop propagation is the traceability mechanism either way:
every edit ripples exactly as far as its declared `related` links, is fully
logged in `catalog.md`, and never requires loading more than a handful of
documents at a time.

Full agent definitions: `.github/agents/10-docs-author.agent.md` and
`.github/agents/11-docs-sync.agent.md`. Shared enforcement rules:
`.github/instructions/docs-traceability.instructions.md`.

---

## 5. How this plugs into the existing 10-agent chain

Every phase agent that produces or changes something doc-worthy (Architect,
Developer, and RE for elaboration-driven interface/data changes) hands off
to `Docs - Author` (new doc) or calls `Docs - Sync` directly (edited an
existing doc in place) as its **last step**, before returning control. No
phase agent edits `catalog.md` or any file under `/docs` directly — that's
always mediated by these two agents, so the reciprocity/update discipline
can't be skipped by an agent that forgot.
