# SDLC Workflow Status: <Initiative Name>

- **Initiative Slug**: `<initiative-slug>`
- **Active Phase**: <Phase Name>
- **Last Updated**: <Date>

## Phase Status Tracker

| # | Phase Name | Responsible Role | Output Artifact | Status | Last Updated | Notes / Comments |
|---|------------|------------------|-----------------|--------|--------------|------------------|
| 1 | Ideation | Product Owner | `sdlc-artifacts/01-budget-pitch/<slug>.md` | Not Started | - | - |
| 2 | Discovery | Product Owner | `sdlc-artifacts/02-business-requirements/<slug>.md` | Not Started | - | - |
| 3 | Requirement Analysis | Requirement Engineer | `sdlc-artifacts/03-it-specification/<slug>.md` | Not Started | - | - |
| 4 | Story Mapping | Requirement Engineer | `sdlc-artifacts/04-story-map/<slug>.md` | Not Started | - | - |
| 5 | Elaboration | Requirement Engineer | `sdlc-artifacts/05-elaborated-stories/<slug>__<story-id>.md` | Not Started | - | - |
| 6 | Design | Architect | `sdlc-artifacts/06-design/<slug>__<story-id>.md` | Not Started | - | - |
| 7 | Implementation Plan | Developer | `sdlc-artifacts/07-implementation-plan/<slug>__<story-id>.md` | Not Started | - | - |
| 8 | Implement (Code + Tests) | Developer | Source Code | Not Started | - | - |
| 9 | Test Design | Test Analyst | `sdlc-artifacts/09-test-cases/<slug>__<story-id>.md` | Not Started | - | - |

## Resume Instructions
To resume the workflow from the current active phase, run the target agent with the following details:
- **Active Agent**: <Target Agent Name (e.g. PO - Discovery)>
- **Context Files**: <List paths to files that need to be read (e.g. sdlc-artifacts/01-budget-pitch/<slug>.md)>
- **Next Instruction**: <Description of what to ask the agent to do next>
