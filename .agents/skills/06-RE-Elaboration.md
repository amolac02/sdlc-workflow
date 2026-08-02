# Skill: User Story Elaboration (06-RE-Elaboration)

## Objective

Expand placeholder user stories from a Story Map into sprint-ready specifications that meet the Definition of Ready (DoR). Ensure strict adherence to Behavior-Driven Development (BDD) syntax, exact technical component mapping, and product-agnostic design.

## Execution Rules & Standards

When elaborating a user story, you must populate it with the following required sections and adhere to these strict formatting rules:

### 1. The Story Statement

* **Format:** `As a [Universal Persona], I need to [Action], so that [Value].`
* **Agnostic Enforcement:** You must use universal actors (e.g., `System Consumer`, `Data Provider`). You are strictly forbidden from referencing legacy platforms (e.g., reportgenerator) or specific client segments.

### 2. Technical Context & Traceability

* **Traceability:** Explicitly list the parent `US-ID`, `IT-REQ`, and `BR-ID`.
* **Component Boundary:** Name the exact C4 `Software Component` and `Sub-component` this story alters.
* **Technical Contract:** If applicable, embed the precise OpenAPI endpoint (e.g., `POST /v1/reports`), database schema changes, or STTM matrix references required to execute the story.

### 3. Acceptance Criteria (BDD / Gherkin)

All business rules and technical constraints must be translated into testable BDD scenarios.

* **Format:** Use strict `Given / When / Then` syntax.
* **Coverage:** You must generate at least one "Happy Path" scenario and at least one "Negative/Edge Case" scenario (e.g., payload exceeds size limit, missing authorization).
* **Architectural Compliance:** If the story involves data privacy or masking, the `Then` statement MUST dictate routing the payload through the centralized **data bridge** for obfuscation. Do not write Acceptance Criteria that apply masking logic directly to frontend UI components or web app prototypes.

### 4. Out of Scope Definition

To prevent scope creep and component bleed during a sprint, you must explicitly define what is *not* being built.

* **Rule:** List at least one logical adjacent feature or component that is explicitly excluded from this story's boundary.

## Validation Before Output

Before finalizing the elaborated story, verify:

1. Are all ACs testable (boolean pass/fail)?
2. Is the story restricted to a single software component?
3. Is it completely product-agnostic?
