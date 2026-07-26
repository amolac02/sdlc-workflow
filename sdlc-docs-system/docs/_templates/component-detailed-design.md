---
id: SVC-XXX-DETAIL                # id of the parent program-summary + '-DETAIL'
type: service-component            # same type as parent
title: <Component Name> — Detailed Design
status: draft
owner: Developer
last_updated: YYYY-MM-DD
tags: []
related:
  parent: [SVC-XXX]                # the program-summary this belongs to — required
  see_also: []
---

## Internal Structure
<Class/paragraph/module structure. For COBOL: paragraph names and PERFORM
chains. For Java: class list and key methods. For UI: component tree.>

## Control Flow
<Step-by-step logic, loops, conditional branches, error handling paths.>

## Notes for Implementers
<Gotchas, known tech debt, things a future change here must not break.>

---
NOT indexed in catalog.md. Reachable only via the `detailed_design` link in
the parent program-summary.md. Only load this when actually modifying the
component's internals (Developer - Implementation Plan / Implement phases).
