# Stage 3 · Parallel sessions and subagents

The AI-Native SDLC Playbook
Parallel sessions and subagents
Lesson 7
4 min

One engineer can drive several streams of work at once.

A parallel session is another full Claude Code instance, working a separate task in its own Git worktree. Each independent session knows nothing about the others, and the engineer steering them is the only thing they share.

A subagent runs inside a single session as a scoped helper with its own context window and tool limits and suits jobs that recur in multiple tasks, such as verifying the app runs as expected.

Parallel sessions raise the number of tasks an engineer can have in flight, while subagents keep each session focused on its own task. The engineer's job is steering and reviewing all of them.

What changes
Traditional	AI-native
One engineer works one task at a time and spends a significant portion of their day/week on builds, tests, and reviews. Switching between tasks while waiting is possible, but the context switch is tiring enough that few people choose to.	One engineer runs several Claude sessions at once, each in its own worktree on its own task. Repeated jobs become subagents with their own context and tool limits. The engineer's job shifts to orchestrating and, eventually, to building and monitoring loops.
Getting started
Prerequisites: The CLAUDE.md, since all sessions read the file. The feedback loop (Stage 4: Test) also helps here, because less supervision from the engineer is needed when a session can verify its own work.
Infrastructure: A Git repository, since isolation comes from worktrees, and permission settings tuned so sessions are not waiting on approval prompts for commands the organization considers safe.
How to execute it
The engineer splits the work into tasks that touch different files, using the plan from the plan mode play (Stage 3: Build) to see where the work is independent. Tasks that share files run in a single session, one after another.
Each parallel task gets its own worktree, for example claude --worktree feature-auth in one terminal and claude --worktree fix-rate-limit in another. A worktree is a separate checkout on its own branch, which stops sessions colliding on files.
Two or three sessions is a sensible starting point. The practical ceiling is how many streams one person can review properly, so add sessions only while review is keeping up.
Turn repeated jobs into subagents, as defined in Markdown files in .claude/agents/, each with a name, a description of when to use it, and the tools it may touch. Examples include a code simplifier that strips needless complexity after the main agent finishes, a verifier that runs the app and checks behavior, and a researcher that explores the codebase and reports back without flooding the main context. Check the definitions into Git so the whole team shares them.
What it looks like

.claude/agents/verifier.md:

markdown
---

name: verifier
description: Runs the app and checks the change works before the session reports done
tools: Bash, Read

---
Start the app with make run. Exercise the changed behavior and the two
nearest neighboring flows. Report what you ran, what you saw, and any
behavior that does not match plan.md. Do not fix anything; report only.
Governance considerations

More sessions means more output, so the controls have to come from configuration in the repo. Hooks and permission settings there apply to all sessions, and what a session does is logged and attributed to the engineer who ran it.

How to measure it
Leading indicator: Concurrent sessions per engineer while review quality holds, counted from the OpenTelemetry export, and the share of the day spent steering rather than waiting.
Lagging indicator: Changes merged per engineer per week read alongside the rework rate as determined per the PR history.
Was this helpful?
Previous lesson
Skills as institutional knowledge
Next lesson
Give Claude a feedback loop
Lesson 7 of 14 · The AI-Native SDLC Playbook
Parallel sessions and subagents
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
