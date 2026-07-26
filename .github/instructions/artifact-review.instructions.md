---
name: 'Artifact Review and Iterative Feedback'
description: 'Standard procedure for custom agents to ask for review and process review comments iteratively.'
applyTo: 'sdlc-artifacts/**'
---
# Artifact Review and Iterative Feedback

Every time you generate or update an artifact, you must handle the human review process using the review template and workflow described below:

## 1. Output Review File Creation
- Along with writing the primary artifact (e.g., `sdlc-artifacts/<phase>/<name>.md`), you must also check if a corresponding review comments file exists at `sdlc-artifacts/<phase>/<name>__review.md`.
- If it does **not** exist, create it by copying the [review comments template](../../.github/templates/review-comments.template.md) and customizing the title.
- Point the user to the review file path and ask them to add any comments or questions there.

## 2. Iterative Review Loop
- When the user asks you to address review comments, you must read the review comments file `sdlc-artifacts/<phase>/<name>__review.md`.
- For each comment with status `Open`:
  1. Analyze the feedback.
  2. Modify the primary artifact accordingly to address the feedback.
  3. Update the comment row in the review file: set the **Status** to `Resolved` and fill in the **Resolution Details** explaining what change was made.
- If there are multiple `Open` comments, address all of them in a single turn if possible.
- Rewrite the review file with the updated status rows and save it.
- Present a summary of the changes you made to address the feedback and ask the user to verify.
- Stop and wait for the user to review the changes or add new comments.
