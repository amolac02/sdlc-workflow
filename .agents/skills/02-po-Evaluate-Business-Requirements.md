# Skill: Evaluate Business Requirements (Evaluate-Business-Requirements)

## Objective

Systematically review and grade drafted Business Requirement Documents (BRDs) or Epics against agile standards, architectural boundaries, and strategic organizational goals before they are approved for IT Requirement Analysis.

## Execution Rules

For any submitted business requirement document, execute a review against the following five gates. If a requirement fails a gate, flag it and provide a corrected rewrite using the appropriate syntax.

### Gate 1: Organization-Wide Applicability (Product-Agnostic Check)

The platform and its agents serve the entire organization. Requirements must not be siloed.

* **Fail Condition:** The text mentions specific client segments, references legacy application names (e.g., AssetLink), or contains logic exclusive to a single product line.
* **Action:** Flag for revision. Rewrite the requirement to abstract the rule so it applies universally to all relevant upstream and downstream consumers.

### Gate 2: Strategic Consolidation

Evaluate the Epic description and Business Value sections.

* **Fail Condition:** The vision statement merely provides a brief summary of overlapping product scopes or accepts the current fragmented state.
* **Action:** Flag for revision. Demand that the business value explicitly emphasizes structural consolidation and the creation of a single, unified service.

### Gate 3: The "What vs. How" Boundary

Ensure the business requirement dictates the capability without imposing unapproved technical implementations.

* **Fail Condition:** The requirement specifies UI elements (e.g., "integrated live features in the web app prototype") or dictates front-end state management.
* **Specific Exception:** If the requirement involves data privacy or masking layers, it *must* align with the established architectural pattern of routing positions through a data bridge for obfuscation.
* **Action:** Strip UI/implementation details and replace them with capability-focused logic.

### Gate 4: Framework Integrity (BDD, JTBD & UML Diagrams)

Check the structural formatting of Epics, specific business rules, and required Mermaid diagrams.

* **Epics:** Must follow Jobs to be Done format (`When [Context], the actor wants to [Action], so they can [Value]`).
* **Rules:** Must use Gherkin syntax (`Given/When/Then`).
* **UML Diagrams:** Must contain a UML Use Case Diagram (represented as Mermaid flowchart `graph LR` mapping actors to ovals `([Use Case])`) in Section 2, and two UML Activity Diagrams (represented as Mermaid flowchart `graph TD`) in Section 3 illustrating As-is and To-be workflows. All Mermaid blocks must be syntactically valid.
* **Action:** Convert traditional statements to BDD; verify or reconstruct missing or invalid Mermaid diagrams.

### Gate 5: The INVEST Matrix

Validate every requirement destined for the User Story Map against the INVEST criteria:

* **I**ndependent
* **N**egotiable
* **V**aluable
* **E**stimable
* **S**mall (Fits within a sprint)
* **T**estable (Definitive pass/fail criteria)
* **Action:** Output a boolean checklist (Pass/Fail) for the overall document against these six criteria.

## Output Format

Generate an Evaluation Report structured as follows:

1. **Gate Summary:** A quick Pass/Fail/Needs Revision for all five gates.
2. **Flagged Violations:** The specific text that failed, the gate it violated, and an automated suggestion on how to rewrite it.
3. **Approval Status:** Recommend "Approved for IT Analysis" or "Return to Product Owner for Revision."
