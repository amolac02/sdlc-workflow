# Skill: Agile Business Requirements (Agile-Business-Requirements)

## Objective

Translate raw discovery data, budget pitches, and stakeholder inputs into modular, Scrum-ready business requirements without dictating technical architecture.

## Methodology Rules

### 1. Epic Definition using Jobs to be Done (JTBD)

When establishing the high-level capabilities (Epics), frame the business value entirely around the actor's goal. Do not use standard user stories at the Epic level.

* **Format:** "When `[Situation/Context]`, the actor wants to `[Motivation/Action]`, so they can `[Expected Outcome/Value]`."
* **Application:** Use this to define the core capability in the Business Value or High-Level Requirements sections of the template.

### 2. Business Rules & Logic using BDD (Gherkin)

When detailing specific business rules, compliance mandates, or operational boundaries, you must use Behavior-Driven Development syntax. This ensures rules are universally understood and ready for the IT Requirements phase.

* **Format:**
  * **Given** `[Precondition/Initial State]`
  * **When** `[Trigger Event/Action]`
  * **Then** `[Expected Result/System Behavior]`
* **Constraint:** The Gherkin logic must remain focused on business behavior, not UI clicks or database state changes.

### 3. Modularity for User Story Mapping

Ensure every requirement outlined can be logically sliced into smaller, independent units.

* Avoid monolithic rules. If a "Then" statement contains multiple distinct business outcomes (e.g., "Then generate a report AND notify the manager AND update the ledger"), split it into separate BDD scenarios.

### 4. Agnostic Rule Validation

Before finalizing any requirement, run the following validation:

1. Does this requirement depend on a specific client segment? (If Yes -> Rewrite to apply universally).
2. Does this requirement dictate a specific technology or platform? (If Yes -> Rewrite to focus purely on the capability).
