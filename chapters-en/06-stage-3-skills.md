# Stage 3 · Skills as institutional knowledge

The AI-Native SDLC Playbook
Skills as institutional knowledge
Lesson 6
4 min

Skills are how an organization makes its institutional knowledge operational. The instructions are explicit, version controlled, applied broadly, and updated centrally when policy changes. The rule of thumb: write a skill for institutional knowledge that must be applied consistently; don't write a skill for components that belong in CLAUDE.md or a prompt.

Getting started
Prerequisites: None required. Having a CLAUDE.md helps, because it keeps the agent's working knowledge in the repo, but a skill does not depend on it.
Infrastructure: One policy with a named owner and a written source of truth.
How to execute it
Pick one piece of knowledge that is enforced inconsistently today. This could be a security standard, an API design convention, or a brand rule.
Write it as a skill, a folder containing a SKILL.md whose frontmatter says when it triggers and whose body says what to do. An engineer writes it from the policy owner's source of truth, using Claude to help.
Put the skill in the repo at .claude/skills/<name>/ so it ships with the code, or distribute it organization-wide through a plugin.
Test that the skill triggers. Ask Claude to do the relevant task in different ways and confirm the skill loads each time.
When the policy changes, change the skill and have the policy owner sign off on the change.
Engineers pick up the new version automatically in their next session.
What it looks like

.claude/skills/secure-api-review/SKILL.md:

markdown
---
name: secure-api-review
description: Apply the API security standard. Use whenever creating or
  modifying an external-facing endpoint, reviewing API code, or
  generating an OpenAPI spec.
---

# Secure API review
When you create or change an API endpoint:
1. Authentication: every endpoint requires the gateway JWT;
   no anonymous routes outside /health.
2. Input validation: validate request bodies against the OpenAPI
   schema and reject unknown fields.
3. Audit: every state-changing endpoint emits an audit event with
   actor, action, entity and timestamp.
4. Data classification: fields tagged pii in the schema must never
   appear in logs or error messages.
Run scripts/check-endpoints.sh and include its output in your summary.
Governance considerations

A skill is a control, though an advisory one. It makes Claude likely to apply the policy while the code is written, and nothing forces a session to comply with it. A policy that must always hold needs something deterministic behind the skill, such as a hook that blocks the action or a review pass that re-checks the policy at the PR. The skill makes violations rare and the hook makes them close to impossible. Skill invocations are logged in session traces, and the policy owner reviews skill changes like code.

How to measure it
Leading indicator: Time from the policy owner approving a policy change to the updated skill merging, taken from the PR on the skill folder.
Lagging indicator: PR review findings that cite the policy, which should fall toward zero once the skill is applying the policy while the code is written. Where the findings don't fall toward zero, either the skill isn't triggering or its text has drifted from the official policy.
Hooks as build-time guardrails

A skill is an advisory control, while a hook is the deterministic layer behind it. Most of Claude's actions are file edits and shell commands during implementation, so the build phase is where hooks can end up firing most often.

Build-phase hooks can:

Block edits to protected paths such as generated classes or a frozen package
Run the formatter and linter after file edits so drift never accumulates
Keep credentials out of the diff
Back any skill whose policy has to hold without exception

A hook runs on each action that matches it, so build-phase hooks should be fast and scoped to the file that changed. Heavier checks such as the full test suite belong at the commit or the PR.

A hook that asks a human for approval belongs with the gates in Stage 5: Deploy, because an approval prompt during the build puts a person back on the critical path of all the sessions running in parallel.

Was this helpful?
Previous lesson
The CLAUDE.md
Next lesson
Parallel sessions and subagents
Lesson 6 of 14 · The AI-Native SDLC Playbook
Skills as institutional knowledge
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
Hooks as build-time guardrails


---
