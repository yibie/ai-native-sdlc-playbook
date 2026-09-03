# Stage 3 · Claude Code plan mode as the default starting point

The AI-Native SDLC Playbook
Claude Code plan mode as the default starting point
Lesson 4
5 min

Engineers start Claude Code sessions in plan mode, give Claude the approved spec.md from Stage 2: Design, and let it interview them, iterating on the plan until they are happy with it.

What changes
Traditional	AI-native
An engineer reads the design and starts writing code. How the change will be made, down to which files and which tests, stays in the engineer's head or at best in a ticket comment. Nobody else can review it. The first thing a reviewer sees is the finished diff, and by then rework is slow.	Work starts with a written plan that Claude produces in plan mode, where it can read the codebase without changing anything. The engineer corrects the plan before code is written, and the approved version is committed as plan.md for later stages to check against.
Getting started
Prerequisites: The intent artifact (intent.md or spec.md) if one exists, and the CLAUDE.md file helps.
Infrastructure: Claude Code with access to the repository.
How to execute it
The engineer starts the session in plan mode with Claude.
The engineer gives Claude the intent.md and the spec.md and asks for an implementation plan that names the files that change, the order of the work, and the tests that prove it.
Interrogate the plan by asking what the change could break, which step is most risky, and what other options Claude chose not to do.
Iterate until an engineer who has never seen the conversation could implement the change from the plan alone.
Commit the approved plan as plan.md. The plan joins the audit trail, and the PR review play (Stage 5: Deploy) checks the eventual diff against it.
Accept the plan and let Claude implement. With a solid plan, the implementation is often a single pass.
When implementation departs from the plan, update plan.md in the same commit. Consider using a hook to enforce synchronization between the two.
What it looks like

plan.md:

markdown

# Plan: claims status self-service (from intent.md 2026-06-02)

## Files that change

portal/src/claims/StatusPanel.tsx (new), claims-api/routes/status.py, claims-api/tests/test_status.py

## Order of work

1. Add the status endpoint behind existing auth.
2. Panel against the endpoint.
3. Wire into the portal nav.

## Risks

The claims-core API rate-limits at 50 rps; the panel must cache.

## Proof

test_status.py covers the four claim states; screenshot matches the approved mock.
Governance considerations

Design review happens before any code is generated, when changing course is still a matter of editing a document. Plan mode enforces this itself, since Claude cannot edit files until the engineer accepts the plan. The plan and its revisions are logged along with who accepted it. Routine changes are approved by the engineer, and anything the organization classes as higher risk goes to a tech lead or architect.

How to measure it
Leading indicator: Share of changes that merge from the first implementation pass, and time from plan approval to merged PR with the required data within the PR metadata.
Lagging indicator: Rework cycles per change, again from the PR metadata, and how often the merged diff still matches the committed plan.md.
Claude Code in auto mode

Claude Code can also run in auto mode, where the engineer iterates on and approves the plan, and Claude then applies each change without a per-edit prompt. As the guardrails from the later plays mature (a tuned CLAUDE.md, skills that encode policy, hooks that block unsafe actions, and a test suite Claude can run), auto mode becomes the default for routine work: a tight spec.md, a small blast radius, and code the tests already cover.

The shift is now away from the user watching the agent make the edits and reviewing actions, toward the review of artifacts after longer autonomous sessions. Auto mode further enables parallelism across individuals and the team when used with worktrees and is fundamental to running the SDLC autonomously and closing the loop as described in Stage 6: Maintain.

Legacy systems and the source of truth

Existing SDLC processes likely already track artifacts, just not in Markdown files. Work items may be in Jira, requirements in a tool with regulatory traceability built in, designs in Figma, and change approvals with a change board. Those systems are hard to displace because auditors and regulators already accept them and other teams depend on them, so the AI-native SDLC has to fit around what exists. For each artifact the process produces, one system should be named the source of truth and the others hold a copy or a link.

The below configurations can be set up to have one source of truth with the choice differing per artifact:

The repo as the source of truth. The Markdown artifacts are the authoritative record, and the legacy system references files within commits. This can be one of the cleanest configurations for engineering-led organizations as all records live in one tool with one timestamp authority.
The legacy system is the truth. Jira, ServiceNow, or the requirements tool holds the authoritative record, and the Markdown artifacts are working copies. Claude reads the record at the start of the session and writes the outcome back through a Model Context Protocol (MCP) connector in the same session that produced the spec or the plan.
Linkage as the minimum bar. All artifacts note the record ID, and all legacy records contain the commit SHA of the Markdown file. The linkage option is a good place to start when transitioning to the AI-native SDLC as there are two sources of truth.

Both the legacy system and the AI-native Markdown-first system can coexist so long as there is a link between the two or one is declared the source of truth.

Was this helpful?
Previous lesson
Requirements and design
Next lesson
The CLAUDE.md
Lesson 4 of 14 · The AI-Native SDLC Playbook
Claude Code plan mode as the default starting point
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
Claude Code in auto mode
Legacy systems and the source of truth


---
