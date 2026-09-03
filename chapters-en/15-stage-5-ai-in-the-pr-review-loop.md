# Stage 5 · AI in the PR review loop

The AI-Native SDLC Playbook
AI in the PR review loop
Lesson 10
4 min

Claude both gives and receives reviews. It reviews incoming PRs against the organization's policies and addresses review comments on its own PRs. This allows engineers to focus on behavior in their PR review, which boils down to judging intent and risk.

What changes
Traditional	AI-native
Review capacity is planned around human output. A PR waits for a reviewer to read all of it, review quality varies with the reviewer's load, and the author chases while the backlog grows.	All PRs get an identical set of review passes, with findings ranked by severity. Human attention moves up a level, to whether the change does what the plan intended and whether the risk is acceptable.
Getting started
Prerequisites: An updated CLAUDE.md file from Stage 3: Build, skills, if the review passes are to enforce written policies, and defined subagents.
Infrastructure: A repo with the Claude integration installed, either the managed Code Review (research preview) service enabled by an admin or the claude-code-action running in your own CI, with model calls through Amazon Bedrock, Google Cloud's Vertex AI, or Microsoft Foundry where needed (the CI/CD play covers the deployment options). Branch protection policies that require a code owner's approval are also worthwhile.
How to execute it
The managed Code Review service is the fastest start. An admin enables it and selects repositories. Run the review in your own CI with the claude-code-action when you need control of the pipeline or want API calls routed through your own cloud agreement (the CI/CD play covers that plumbing).
The tech lead writes the review policy as REVIEW.md at the repo root, divided into the passes the organization cares about: bugs and logical errors; security and vulnerabilities; compliance against the spec (spec.md from the requirements play), the implementation plan (plan.md from the plan mode play), and design principles. REVIEW.md also defines what counts as Important as opposed to a Nit, and what to skip.
The tech lead sets the human threshold. Findings do not approve or block a PR on their own, and branch protection still requires approval from a code owner. A platform engineer who wants to gate merges on findings can read the severity counts that the check run publishes as a machine-readable tally.
When a reviewer or the author tags @claude on a review comment, Claude addresses the comment and pushes the fix. The PR thread records both the request and the change. This fix loop runs through the claude-code-action. In the managed service, commenting @claude review requests a fresh review instead. For PRs Claude opened, go further and let Claude babysit the PR to merge. Teams wrap the loop in a custom slash command that sweeps the unresolved review comments and failing checks on the PR, addresses them, and pushes the fixes, until the PR is green and waiting only on code owner approval.
Review findings feed back into CLAUDE.md. When a review flags a mistake for the second time, the correction goes into CLAUDE.md as part of that review, and because review reads CLAUDE.md, the mistake is caught from the next PR onwards. Review also flags when a change has made CLAUDE.md outdated.
Once a month the tech lead tunes the setup by rating findings so the reviewer improves and by capping Nit volume in REVIEW.md. Generated paths and anything CI already enforces are excluded.
What it looks like

REVIEW.md:

markdown