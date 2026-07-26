# Skill: Story Mapping & Definition (04-RE-Story-Mapping)

## Objective

Translate structural IT Specifications into a chronological, two-dimensional User Story Map. Generate unelaborated user stories that are strictly mapped to software components, completely product-agnostic, and sliced for iterative Scrum delivery.

## Execution Rules & Standards

### 1. The Agnostic Backbone (Chronological Flow)

The horizontal axis of the story map represents the lifecycle of the data or the user journey.

* **Sequential Logic:** Organize Backbone Activities from inception to completion (e.g., `Data Ingestion` -> `Validation` -> `Privacy Obfuscation` -> `Aggregation` -> `Delivery`).
* **Agnostic Personas:** You must never use specific client segments, legacy product names (e.g., AssetLink), or target niche units. Use universal actors like `System Consumer`, `Data Provider`, or `Internal Auditor`.

### 2. Component-Driven Slicing (The Vertical Axis)

Stories must be derived directly from the IT-REQs and sliced according to the provided `code-hierarchy.instructions.md`.

* **The Golden Rule of Slicing:** One User Story = One Component (or Sub-component).
* **Example:** If `IT-REQ-05` requires an API endpoint and a database schema change, you must split this into two separate stories (e.g., `US-10` for the API component, `US-11` for the DB component).
* **Cross-Component Flag:** If a story absolutely cannot be split and must span multiple components, you MUST flag it in the output table with `⚠️ YES - Review for Split`.

### 3. Unelaborated Story Format (JTBD Standard)

Because this is the mapping phase (prior to elaboration), stories should strictly define the boundary and value, but exclude detailed Acceptance Criteria or BDD scenarios.

* **Format:** `As a [Universal Persona], I need to [Action mapped to Component], so that [Value mapped to IT-REQ].`
* **Example:** "As a System Consumer, I need the Outbound API component to paginate payloads exceeding 50MB, so that memory limits are not breached."

### 4. Release Slicing (The Horizontal Boundaries)

Group the vertical stories into logical deployment slices.

* **Slice 1 (The Walking Skeleton):** Identify the absolute minimum stories across the entire backbone required for a single, end-to-end payload to process successfully. (e.g., hardcoded data passing through the data bridge and out the API).
* **Slice 2+ (Enhancements & Edge Cases):** Group subsequent stories dealing with complex error handling, alternative flows, or expanded data parameters.

### 5. Architectural Integrity Checks

* **Data Obfuscation:** If the IT Spec mentions data masking or privacy, ensure stories map to the centralized **data bridge** component for routing, strictly avoiding any stories that attempt to build masking logic directly into UI components or web app prototypes.
* **Traceability Validation:** Before generating the final document, verify that `US-ID` --> `IT-REQ` --> `Software Component` is an unbroken chain for every single story. Delete any generated story that lacks an `IT-REQ` parent.

## Template Population Guidance

When populating the `04-story-map.template.md`, prioritize extreme clarity in the mapping table. The engineering pod must be able to look at the table and immediately know which component they are working in and which IT requirement they are satisfying, without needing to cross-reference multiple documents.
