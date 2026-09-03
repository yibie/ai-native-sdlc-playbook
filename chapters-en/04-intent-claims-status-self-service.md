# Intent: claims status self-service
Author: J. Ortiz (claims operations). Status: draft.
## Problem
Customers phone the contact center to ask where their claim is.
Handlers spend roughly a third of call time on status-only queries.
## Proposed outcome
Customers see claim status, next step and expected date in the portal.
## Affected users and systems
Claims handlers, portal team, claims-core API.
## Constraints
No new PII in the portal session. Existing authentication only.
## Open questions
Do third-party loss adjusters need access too?
Governance considerations

The evidence is the committed intent.md, which lists the author, the timestamp, and the full revision history. It's logged in the Git history of the intent home. The product owner approves, and the accept or reject decision that sends the intent into Stage 2: Design is recorded as the merge or the closing review.

How to measure it
Leading indicator: Time from first conversation to a committed intent.md, read from Git history on the intent home, which records author and timestamp. The expectation is for this to fall from a multi-week elicitation and refinement cycle to hours.
Lagging indicator: The survival rate, or the share of intent.md files that the product owner accepts into Stage 2: Design rather than closes. The accept or reject decision is recorded as the merge of the artifact or the closed review. Additionally, count the changes to intent.md made after the first spec.md commit for the same change.
Was this helpful?
Previous lesson
Introduction
Next lesson
Requirements and design
Lesson 2 of 14 · The AI-Native SDLC Playbook
Capture as intent.md
Introduction
Introduction
Stage 1: Plan
Capture as intent.md
Stage 2: Design
Requirements and design
Stage 3: Build
Claude Code plan mode as the default starting point
The CLAUDE.md
Skills as institutional knowledge
Parallel sessions and subagents
Stage 4: Test
Give Claude a feedback loop
Continuous evals in CI
Stage 5: Deploy
AI in the PR review loop
Hooks as approval gates
CI/CD integration and deployment
Stage 6: Maintain
Closing the loop on metrics
Closing
Closing thoughts and resources
Course complete
What changes
Getting started
How to execute it
What it looks like
Governance considerations
How to measure it


---
