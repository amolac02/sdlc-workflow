# Skill: Evaluate Elaborated Stories (07-Evaluate-Elaborated-Stories)

## Objective

Systematically audit elaborated user stories against the INVEST criteria and the Definition of Ready (DoR). Ensure Acceptance Criteria are completely testable, component boundaries are strictly maintained, and architectural rules are enforced before human review.

## Execution Rules (Evaluation Gates)

Run every drafted user story through these five gates. If a story fails any gate, explicitly flag the failure and execute the required corrective action.

### Gate 1: Independent & Small (The INVEST Check)

* **Check:** Does the story deliver a complete, testable vertical slice within a *single* software component (e.g., just the Outbound API, or just the Data Ingestion batch)? Is it small enough to be completed in one sprint?
* **Fail Condition:** The story spans multiple components, or has complex, cascading dependencies (e.g., "Cannot test the API until the UI is built").
* **Action:** Force a split. Break the story into smaller independent stories (e.g., `US-12a` for the API, `US-12b` for the UI) and mock the dependencies in the Acceptance Criteria.

### Gate 2: Testable ACs & UML Diagrams (The BDD & Diagram Check)

* **Check:** Are all Acceptance Criteria written in strict `Given/When/Then` format? Are the outcomes binary (Pass/Fail)?
* **Check:** Ensure there is a UML State Machine Diagram (using Mermaid `stateDiagram-v2`) showing core entity state changes. For technical/Enabler stories, ensure a UML Sequence Diagram (using Mermaid `sequenceDiagram`) is also present. All diagrams must be syntactically valid.
* **Fail Condition:** ACs contain subjective adjectives ("fast", "seamless"), lack a negative/edge case, or diagrams are missing/invalid.
* **Action:** Rewrite subjective ACs. Generate or correct the Mermaid state/sequence diagrams to model the flow properly.

### Gate 3: Valuable & Product-Agnostic

* **Check:** Does the story provide inherent business or technical value without relying on legacy silos?
* **Fail Condition:** The story explicitly names specific client segments, uses legacy platform names (e.g., reportgenerator), or builds point-to-point hardcoded logic.
* **Action:** Rewrite the story statement and ACs to use universal parameters and actors (e.g., `System Consumer`, `Data Provider`).

### Gate 4: Architectural Integrity (Data Bridge Pattern)

* **Check:** If the story involves data privacy, masking, or obfuscation, review the `Then` clauses in the ACs.
* **Fail Condition:** The story attempts to implement masking logic directly within UI components or web application prototypes.
* **Action:** Rewrite the ACs to explicitly enforce that the payload is routed through the centralized **data bridge** component for obfuscation.

### Gate 5: Explicit Boundaries

* **Check:** Does the story have a defined "Out of Scope" section?
* **Fail Condition:** The section is missing or empty.
* **Action:** Generate an explicit out-of-scope statement listing at least one adjacent feature or component that is not included in this ticket to prevent scope creep.

## Output Format

Generate a Story Elaboration Audit Report structured as follows:

1. **Gate Summary:** Pass/Fail status for the five gates across the batch of stories.
2. **Flagged Violations & Corrections:** The specific `US-ID` that failed, the violated gate, and the exact rewrite or split applied.
3. **Approval Status:** Recommend "DoR Met - Ready for Sprint" or "Return to Elaboration."
