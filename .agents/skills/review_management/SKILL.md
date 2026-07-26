---
name: review_management
description: Rules for managing user review comments and updating their status iteratively during artifact generation.
---
# Review Management Skill

Always use this skill when executing the review phase of any development stage:

## 1. Initiating Review
- Whenever you generate an output artifact, you must also prepare or locate a review comments file:
  - Initiative-level: `sdlc-artifacts/<phase-folder>/<initiative-slug>__review.md`
  - Per-story: `sdlc-artifacts/<phase-folder>/<initiative-slug>__<story-id>__review.md`
- If it does not exist, create it from [.github/templates/review-comments.template.md](../../.github/templates/review-comments.template.md).
- Prompt the user to review the generated artifact and log any feedback directly in the review comments file.

## 2. Iterative Updates
- If the user provides review feedback or updates the review comments file:
  1. Read the review comments file.
  2. Perform the required code or configuration changes to address all `Open` comments.
  3. Update the comment status in the review table to `Resolved` and document the resolution details.
  4. Write the updated review comments table back to the file.
- Repeat this process iteratively until all comments are marked as `Resolved` and the user signs off.
