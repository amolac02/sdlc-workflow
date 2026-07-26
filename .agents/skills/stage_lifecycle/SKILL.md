---
name: stage_lifecycle
description: Guidelines and steps for managing the lifecycle of each development stage, including planning, verification, and interactive MCQ feedback.
---
# Stage Lifecycle Management

Always follow these steps when executing any stage of development in this workspace:

## 0. Entrypoint and Orchestration
- Every new SDLC initiative run or workflow resumption should begin by running the `@SDLC - Orchestrator` agent in VS Code.
- The orchestrator will parse the status file (`sdlc-artifacts/workflow-status.md`) and direct you to the correct active phase and handoff.

## 1. Summarize Implementation Plan
Before making any source code modifications or configuration changes:
- Create or update the `implementation_plan.md` in the current conversation's artifact directory.
- Summarize the key changes, components touched, and files to create or modify.
- Explicitly ask the user to review the implementation plan and approve it.
- **Do not make any actual changes or execute modifying commands until the user has explicitly approved the plan.**

## 2. Execute Approved Changes
Once the user approves the implementation plan:
- Create or update `task.md` to track the detailed step-by-step tasks.
- Perform the modifications, file creation, or refactoring exactly as approved.
- Track progress by marking items as `[/]` (in progress) or `[x]` (completed) in `task.md`.

## 3. Present Walkthrough
After finishing the changes:
- Create or update the `walkthrough.md` artifact to list all accomplishments.
- Detail the files changed, new structures created, and verification results.

## 4. Document Open Questions & Feedback (MCQ Style)
If there are any open questions, design choices, or issues that need the user's attention:
- Document them in `open_questions.md` inside the active artifact directory: `C:\Users\rupam\.gemini\antigravity-ide\brain\<conversation-id>\open_questions.md`.
- Present the questions to the user in a clear Multiple Choice Question (MCQ) format.
- Every MCQ must include:
  - Selectable options representing distinct paths/decisions.
  - A final option allowing the user to provide free-text feedback.
- Prompt the user to answer the questions or add their feedback directly to the `open_questions.md` file.

## 5. Artifact Review Loop
- Once output artifacts are generated, the artifact review sub-lifecycle starts, governed by the [review_management](../review_management/SKILL.md) skill.
- Create or update the review comments file (e.g. `<artifact-name>__review.md`) in the same folder.
- Prompt the user to review and record feedback in that file.

## 6. Iterative Refinement
- After the user provides feedback (via chat, `open_questions.md`, or the `__review.md` feedback file), address the comments iteratively.
- Resolve and mark review comments as `Resolved` in the review table using the [review_management](../review_management/SKILL.md) skill guidelines.
- Repeat the review and verification cycle until all review comments are addressed, no more open questions remain, or the user explicitly asks to stop and finalize the outcome.
