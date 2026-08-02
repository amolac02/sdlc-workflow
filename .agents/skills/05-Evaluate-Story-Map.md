# Skill: Evaluate Story Map (05-Evaluate-Story-Map)

## Objective

Systematically review the drafted Story Map to ensure unbroken traceability, strict component boundaries, release viability, and adherence to architectural patterns before it is presented for human review.

## Execution Rules (Evaluation Gates)

For any generated Story Map, execute a review against the following four gates. If the map fails a gate, flag the specific stories and provide instructions for how to split, rewrite, or sequence them correctly.

### Gate 1: Traceability, Persona Agnosticism & UML Backbone

* **Check:** Every `US-ID` must map to a valid `IT-REQ`.
* **Check:** The persona driving the story must be universal (e.g., `System Consumer`, `Data Provider`).
* **Check:** Ensure there is a UML Activity Diagram (represented as Mermaid flowchart `graph LR` syntax) chronologically mapping the backbone activities.
* **Fail Condition:** Stories lacking an `IT-REQ` parent, stories mentioning legacy segments (e.g., reportgenerator), or a missing/invalid Mermaid backbone flow diagram.
* **Action:** Delete orphaned stories (scope creep). Rewrite persona actors to remain strictly product-agnostic. Generate or correct the Mermaid backbone flow diagram.

### Gate 2: Component Boundary Limits (Vertical Slicing)

* **Check:** Verify the `Software Component` mapped to each user story.
* **Fail Condition:** A single story dictates changes across multiple distinct deployable units (e.g., attempting to modify a React frontend, a Java API, and a DB schema in one `US-ID`).
* **Action:** Force a split. Break the multi-component story into separate, component-specific stories vertically aligned under the same Backbone Activity. If it absolutely cannot be split, ensure the `Cross-Component Flag` is set to `⚠️ YES - Review for Split`.

### Gate 3: The "Walking Skeleton" (Release 1 Viability)

* **Check:** Review the stories grouped under "Slice 1" or "Release 1".
* **Fail Condition:** Slice 1 does not contain at least one story for *every* Backbone Activity from inception to output. (e.g., Ingestion is built, but delivery is missing).
* **Action:** Re-sequence the map. Pull complex, edge-case validation stories down into Slice 2, and promote basic, hard-coded delivery stories up into Slice 1 so the system can run end-to-end.

### Gate 4: Architectural Pattern Enforcement

* **Check:** Evaluate stories dealing with data masking, privacy, or obfuscation.
* **Fail Condition:** Privacy or masking logic is mapped to frontend UI components or web app prototypes.
* **Action:** Delete the story and remap the requirement to the **data bridge** component, enforcing the centralized obfuscation routing pattern.

## Output Format

Generate a Story Map Evaluation Report structured as follows:

1. **Gate Summary:** Pass/Fail status for the four gates.
2. **Flagged Violations & Corrections:** The specific `US-ID` that failed, the gate violated, and the automated fix applied (e.g., "Split US-10 into US-10a [API] and US-10b [DB]").
3. **Approval Status:** Recommend "Approved for Elaboration" or "Return for Structural Review."
