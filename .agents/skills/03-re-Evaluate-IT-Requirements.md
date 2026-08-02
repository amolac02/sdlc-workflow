# Skill: Evaluate IT Requirements (Evaluate-IT-Requirements)

## Objective

Systematically review and grade drafted IT Specification documents against architectural hierarchy, tracing rules, standard interface conventions, and Mermaid UML correctness before they are approved for Story Mapping.

## Execution Rules

For any submitted IT Specification document, execute a review against the following five gates. If a specification fails a gate, flag it and provide a corrected rewrite or structural fix.

### Gate 1: Traceability Verification
* **Check:** Every `IT-REQ` must map back to a valid `BR-ID`.
* **Fail Condition:** Technical requirements that cannot be traced to a parent business requirement (scope creep).
* **Action:** Flag and delete or request clarification on the parent business goal.

### Gate 2: Component Hierarchy Alignment
* **Check:** All referenced components and sub-components must exist in `code-hierarchy.instructions.md`.
* **Fail Condition:** Arbitrary component names, typos, or legacy names (e.g. reportgenerator) not present in the code hierarchy.
* **Action:** Correct the mapping to use exact component/sub-component names verbatim.

### Gate 3: Diagram Completeness & Syntax Check
* **Check:** Ensure both a UML Component Diagram (Mermaid `graph TB`) and a UML Sequence Diagram (Mermaid `sequenceDiagram`) are present and syntactically correct.
* **Fail Condition:** Diagrams are missing, incomplete, or contain invalid Mermaid syntax (such as unquoted parentheses in node text or unmatched brackets).
* **Action:** Flag the errors and write a syntactically correct and complete Mermaid block.

### Gate 4: Centralized Privacy Routing (Data Bridge Pattern)
* **Check:** If business requirements mandate data privacy, masking, or obfuscation, the sequence diagram must route interactions through the centralized data bridge component.
* **Fail Condition:** Obfuscation or masking logic is direct between the source and target components without a data bridge hop.
* **Action:** Rewrite the sequence diagram to include the data bridge as a mediator actor routing the payloads.

### Gate 5: Product-Agnostic Technical Contracts
* **Check:** Verify that API contracts (OpenAPI schema/payloads) and database schemas are defined using universal capability parameters.
* **Fail Condition:** Payloads or schemas contain hardcoded specific client segments or legacy system keys.
* **Action:** Abstract parameters into standard, universal fields.

## Output Format

Generate an IT Specification Evaluation Report structured as follows:

1. **Gate Summary:** Pass/Fail/Needs Revision for all five gates.
2. **Flagged Violations:** The specific section/diagram that failed, the gate it violated, and an automated suggestion on how to rewrite or fix it.
3. **Approval Status:** Recommend "Approved for Story Mapping" or "Return to Requirement Engineer for Revision."
