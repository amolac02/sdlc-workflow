# Skill: Agile IT Requirements (Agile-IT-Requirements)

## Objective

Translate validated, product-agnostic Business Requirements into modular technical contracts, architectural boundaries, and data structures mapped directly to software components.

## Execution Rules & Standards

When generating the `03-it-specification/<initiative-slug>.md` document, you must apply the following technical standards to populate the template sections.

### 1. API-First Contracts (OpenAPI Standard)

When mapping requirements to interface components (e.g., between a ReactJS frontend and a Java Spring Boot backend, or B2B outbound reporting APIs), define the contract explicitly.

* **Application:** Use OpenAPI/Swagger structural concepts in the specification. Define the exact endpoints (e.g., `GET /v1/reports/outbound`), expected JSON request/response payloads, and HTTP status codes.
* **Rule:** The API specification must remain entirely product-agnostic. Rely on universal identifiers rather than specific legacy system keys.

### 2. Component Interaction (UML Sequence Diagrams)

Use sequence mapping to document exactly how the identified software components interact to fulfill a business rule, particularly for asynchronous flows or security routing.

* **Application:** When writing the "Solution Boundary" or interaction sections, use **Mermaid.js** syntax to generate sequence diagrams.
* **Example Scenario:** If a business requirement dictates data privacy, the sequence diagram must explicitly show the data payload routing from the source component, through the centralized **data bridge** for obfuscation/masking, and finally to the outbound consumer.

### 3. Source-to-Target Data Mapping (STTM)

For any requirements involving data pipelines, reporting generation, or data migration, narrative descriptions are insufficient.

* **Application:** Generate a strict Data Mapping Matrix.
* **Format:** Columns must include `Source Component`, `Source Field`, `Target Component`, `Target Field`, `Data Type`, and `Transformation Logic`.
* **Boundary Conditions:** Explicitly note any transformation logic required for regional compliance (e.g., applying Swiss or European regulatory data localization rules before persisting to the target component).

### 4. Architectural Boundaries (C4 Model)

When filling out the "Software Component" and "Software Sub-component" mappings, adhere to the logic of the C4 Model (Context and Containers).

* **Application:** Do not map requirements to abstract concepts. Map them to specific deployable units (Containers) and their internal modules (Components) as defined in the `code-hierarchy.instructions.md`.
* **Impact Radius:** If an initiative consolidates separate reporting services into a single unified platform, you must explicitly document the C4 "Context" level impact — identifying all upstream data providers and downstream consumers that must point to the new unified component.

### 5. Architecture Decision Records (ADRs)

If translating the business requirement forces a choice between two technical implementations (e.g., choosing synchronous REST vs. asynchronous event streams for a high-volume data pipeline), document it.

* **Application:** Append a mini-ADR structure (Context, Options Considered, Decision, Consequences) to the `IT-REQ` to justify the chosen technical design to the engineering pod.

## Validation Checklist Before Output

Before finalizing the IT Specification, the agent must verify:

1. **Traceability:** Does every OpenAPI endpoint, sequence diagram, and STTM matrix map back to a specific `BR-ID`?
2. **Component Vocabulary:** Are all named systems in the sequence diagrams and C4 mappings pulled verbatim from `code-hierarchy.instructions.md`?
3. **Product Agnostic:** Is the technical design free of specific client segment logic, relying instead on universal capability parameters?
