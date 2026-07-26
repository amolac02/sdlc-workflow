---
id: SVC-XXX                      # or UI-XXX / BATCH-XXX
type: service-component          # ui-component | service-component | batch-component
title: <Component Name>
status: draft
owner: Developer
last_updated: YYYY-MM-DD
tags: []
related:
  implements: []                 # capability id(s) this realizes
  consumes: []                   # EXT-* / SVC-* ids this calls
  produces: []                   # EXT-* ids / downstream this emits to
  data_models: []
  detailed_design: <path-to-detailed-design.md>   # explicit one-hop link, not in catalog.md
  see_also: []
---

## Purpose
<What this component does, one paragraph, business-readable.>

## Inputs / Outputs
<For services: request/response payload shape (or DATA-* reference). For UI:
route definitions + state approach. For batch: job triggers + input/output
files.>

## External Calls
<Other components/interfaces this calls out to — reference by id only.>

## Notes
<Anything a consumer needs to know without opening detailed-design.md —
known limitations, rate limits, deprecation plans.>
