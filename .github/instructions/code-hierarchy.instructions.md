---
name: 'Code / Component Hierarchy'
description: 'Product → Software Component → Software Sub-component taxonomy used to scope requirements, stories, design, and implementation. Referenced manually from agents — not auto-applied.'
---
# Code / component hierarchy

```
Product
└── Software Component        — logical section of the product (e.g. a frontend providing UI)
    └── Software Sub-component — a more specific responsibility within the parent component, e.g.:
        - Java or COBOL modules
        - Service/API definitions exposed by Java/COBOL programs
        - Batch processing flows (mainframe: TWS applications → JCL jobs)
        - Data models (e.g. DB2 tables)
```

User stories and enabler stories are scoped at **either** the component level **or** the sub-component level — there's no fixed rule; it depends on how the specific system is actually built. Don't assume one granularity applies everywhere in the product.

## Mandatory documentation check

Any activity that maps a requirement, story, design, or implementation to component(s) needs documentation describing this hierarchy for the system(s) in scope: which components exist, and which sub-components (modules, service/API definitions, batch flows, data models) belong to each.

**If this documentation is missing, incomplete, or you can't locate it:**
1. Stop before mapping anything to a component/sub-component.
2. Ask the user for the documentation, or ask whether it should be generated from the code repository instead.
3. Do not infer the hierarchy from scattered `search/codebase` results and proceed anyway — a wrong or incomplete component map propagates through every downstream phase (design, implementation plan, test scope) and is expensive to unwind later.
