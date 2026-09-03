# Payments service
## Commands
- Build: make build
- Test: make test (unit), make itest (integration, needs docker)
- Lint: make lint (runs in CI; fix before pushing)
## Conventions
- Java 21, Spring Boot 3. No new Lombok.
- Money is always BigDecimal, never double.
- Every endpoint needs an integration test in src/itest.
## Architecture
- api/ holds REST controllers, core/ holds domain logic,
  adapters/ talks to external systems.
- Kafka events are defined in schemas/; never edit generated classes.
## Things Claude gets wrong
- Do not bump dependency versions; the platform team owns them.
- The legacy v1/ package is frozen; changes go in v2/.
Governance considerations

CLAUDE.md is version controlled, so the instructions the agent works to are reviewable and auditable. Team conventions are applied through the file, changes to it are logged in Git history, and code owners approve those changes in PR review.

How to measure it
Leading indicator: How often Claude repeats a mistake CLAUDE.md should have caught. The corrections or changes to the CLAUDE.md should be tracked within the Git history.
Lagging indicator: Time to first merged PR for a new member of the team from PR history.
Was this helpful?
Previous lesson
Claude Code plan mode as the default starting point
Next lesson
Skills as institutional knowledge
Lesson 5 of 14 · The AI-Native SDLC Playbook
The CLAUDE.md
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
Getting started
How to execute it
What it looks like
Governance considerations
How to measure it


---
