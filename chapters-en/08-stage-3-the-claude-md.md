# Stage 3 · The CLAUDE.md

The AI-Native SDLC Playbook
The CLAUDE.md
Lesson 5
3 min

CLAUDE.md gives Claude the context a new joiner would need, covering conventions, commands, architecture, and the mistakes the team sees most often. Knowledge that used to sit in people's heads and on wikis becomes a file the agent reads at the start of every session, maintained by the whole team and iterated on whenever a mistake is made.

Getting started
Prerequisites: None.
Infrastructure: A repo, Claude Code installed, and one engineer who knows the codebase well.
How to execute it
Run /init in the repo. Claude generates a starting CLAUDE.md from what it finds.
Cut the generated file down to what a new joiner would need on day one. Keep the build, test, and lint commands, the conventions that matter, and the things Claude keeps getting wrong.
Check CLAUDE.md into Git at the repo root so the whole team shares one version and changes are reviewed like code.
A working rule helps here. When Claude makes a mistake twice, the correction goes into CLAUDE.md.
Keep it under a page, because Claude reads all of it at the start of a session and anything stale is taking up context for no benefit.
What it looks like

CLAUDE.md:

markdown