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