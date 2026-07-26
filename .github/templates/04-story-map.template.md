# Story Map: <Initiative Name>

## 1. Backbone Activities

*List the end-to-end journey or system process steps chronologically (left-to-right).*

1. <Activity 1: e.g., Data Ingestion / User Trigger>
2. <Activity 2: e.g., Core Processing / Validation>
3. <Activity 3: e.g., Output / Downstream Handoff>

### Chronological Backbone Flow (UML Activity Diagram)
```mermaid
graph LR
    %% Map the backbone activities sequentially from left-to-right (LR)
    %% e.g., step1[Data Ingestion] --> step2[Validation] --> step3[Downstream Handoff]
```

## 2. Story Stack

*Stack stories under each backbone activity. Prioritize vertically (Critical/High at the top).*

| Backbone Activity | Story ID | Traceability (IT-REQ) | Story Title | Component / Sub-component | Priority | Cross-Component Flag |
|-------------------|----------|-----------------------|-------------|---------------------------|----------|----------------------|
| <Activity Name>   | US-1     | IT-REQ-1              | <Title>     | <Component>               | [Critical] | [None] |
| <Activity Name>   | US-2     | IT-REQ-2              | <Title>     | <Component A, Component B>| [High]     | ⚠️ **YES - Review for Split** |

*Note: Any story spanning multiple components is flagged in the rightmost column for review during Elaboration.*
