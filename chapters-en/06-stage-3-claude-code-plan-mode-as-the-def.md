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