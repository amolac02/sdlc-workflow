---
name: 'SDLC Artifact Conventions'
description: 'Naming, location, and context-discipline rules for SDLC artifacts produced by the PO / RE / Architect / Developer / Test Analyst agents'
applyTo: 'sdlc-artifacts/**'
---
# SDLC artifact conventions

## Folder map
- `sdlc-artifacts/01-budget-pitch/` — Product Owner ideation output
- `sdlc-artifacts/02-business-requirements/` — Product Owner discovery output
- `sdlc-artifacts/03-it-specification/` — Requirement Engineer analysis output
- `sdlc-artifacts/04-story-map/` — Requirement Engineer story mapping output
- `sdlc-artifacts/05-elaborated-stories/` — Requirement Engineer elaboration output (one file per story)
- `sdlc-artifacts/06-design/` — Architect design output (one file per story)
- `sdlc-artifacts/07-implementation-plan/` — Developer implementation plan output (one file per story)
- `sdlc-artifacts/09-test-cases/` — Test Analyst output (one file per story)

Note: code produced by the Developer role lives in the actual source tree, not under `sdlc-artifacts/`.

## Naming
`sdlc-artifacts/<phase-folder>/<initiative-slug>.md` for initiative-level docs (pitch, business requirements, IT spec, story map).
`sdlc-artifacts/<phase-folder>/<initiative-slug>__<story-id>.md` for per-story docs (elaboration, design, implementation plan, test cases) — this lets you trace every artifact for a story back to the same slug across phases.

## Context discipline (this is a hard rule, not a suggestion)
Every agent in this workflow is deliberately scoped to ONE artifact at a time to keep AI credit usage low:
- Read only the specific artifact file(s) the user references in their prompt (by path or story ID). Do not scan the whole `sdlc-artifacts/` folder or the whole repository unless the user explicitly asks for a repo-wide operation.
- If the user's prompt doesn't clearly identify which file(s) to read, ask for the path/story ID before doing anything else — don't guess by searching broadly.
- Write the output to the correct folder per the map above, using the naming convention above. Do not rewrite or "helpfully" touch earlier-phase artifacts.
- Stop once the artifact for this phase is written. Do not chain into the next phase's work yourself — the user reviews, then explicitly moves on (via the handoff button or a new chat).

## Workflow Status Tracking & Resuming
- There is a central status tracking file at `sdlc-artifacts/workflow-status.md` indicating progress.
- Whenever you start or complete a phase, you must update the `sdlc-artifacts/workflow-status.md` file:
  1. Initialize it using the [workflow-status template](../../.github/templates/workflow-status.template.md) if it does not exist.
  2. Mark the status of the current phase as `In Progress` when you start, and `Completed` (or `Under Review` if feedback is open) when you finish.
  3. Set the `Active Phase` and `Last Updated` date at the top.
  4. Update the **Resume Instructions** section to specify the target agent, input files, and next instructions needed to resume/continue the workflow.
