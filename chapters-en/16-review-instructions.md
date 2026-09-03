# Review instructions
## Passes
Run three passes and tag each finding with its pass:
- Bugs: logic errors, broken edge cases, subtle regressions
- Security: injection risks, authentication gaps, PII in logs
- Compliance: the change matches spec.md, plan.md and our design principles
## What Important means here
Reserve Important for findings that would break behavior, leak data
or breach a policy. Style and naming are nits.
## Cap the nits
Report at most five nits per review; summarize the rest as a count.
## Do not report
Generated files under src/gen/ and anything CI already enforces.
Governance considerations

Separation of duties is preserved, because the agent that wrote the code has no way to approve it. The review policy in REVIEW.md is applied to all PRs, and findings, fixes, ratings, and approvals are logged in the PR history, so the PR is the audit record. Approval comes from a human through branch protection, informed by the findings.

How to measure it
Leading indicator: Time to first review, which should fall to minutes, and the share of review comments resolved without a human touching the branch, with data stored directly on Git.
Lagging indicator: Defects and vulnerabilities caught before merge set against those escaping to production, from the PR history and the incident tracker.
Was this helpful?
Previous lesson
Continuous evals in CI
Next lesson
Hooks as approval gates
Lesson 10 of 14 · The AI-Native SDLC Playbook
AI in the PR review loop
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
