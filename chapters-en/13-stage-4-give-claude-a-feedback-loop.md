# Stage 4 · Give Claude a feedback loop

The AI-Native SDLC Playbook
Give Claude a feedback loop
Lesson 8
4 min

Always give Claude a way to verify its own work, whether tests, a build, or a screenshot diff. A session checks its own work and fixes its own mistakes before an engineer sees them.

The feedback loop should not be confused with a verifier subagent (Stage 3: Build). The feedback loop runs through the whole task as many times as the work requires. The verifier subagent, on the other hand, is one way to package the final check by running a fresh context window once the session believes the work is done. This way the verdict is not colored by the assumptions that produced the code.

What changes
Traditional	AI-native
The signal that code works arrives late. CI minutes later, a tester days later, production weeks later. With an agent producing the code, a late signal means a person has to check all of its output, and that person becomes the bottleneck.	The session is given a way to check its own work before a person sees it. Run the tests, run the build, take the screenshot. Claude iterates until the check passes, so what reaches the engineer has already passed it. Setting the loop up falls to the engineer running the session, and the steps below are written for them.
Getting started
Prerequisites: None.
Infrastructure: A test suite and a build that run locally with one command each. For the UI work, a way for Claude to see the result is crucial, either a browser tool or a screenshot utility wired in via MCP.
How to execute it
If checking the work today takes a sequence of commands and some environment knowledge, wrap it in a single target such as make test or npm test that exits non-zero on failure.
In the CLAUDE.md's Commands section, list each command with an example of a healthy output.
State a target and make it quantifiable so Claude can check the work without asking you, for example: "All tests in test_status.py pass," "the screenshot matches the attached mock," or "the endpoint returns 200 with the new field."
For bug fixes, write the failing test first. Ask Claude to reproduce the bug as a test, run it, and confirm it fails for the reason you expect. Commit that test. Only then ask Claude to make it pass without editing the test, with the test-file hook from the final step enforcing the restriction. A test that existed before the fix, and that the agent couldn't rewrite, is proof the bug is gone.
For UI work, close the loop with a visual check. Give Claude a browser or screenshot tool, give it the mock, and let it iterate. Implement, screenshot, compare, and adjust. Two or three rounds is normal, and the result should improve with each one.
Make verification part of "done." The instruction lives in CLAUDE.md: "Run the tests before reporting a task complete, and show the output."
Finally, the loop itself needs protecting, because an agent fixing code must not be able to weaken the check on that code. A hook that blocks edits to test files during a fix task does this. The alternative is to check the diff in review and reject any change that touches a test.
What it looks like

CLAUDE.md verification block:

markdown
## Verifying your work

- Build: make build (must finish with "Build succeeded")
- Test: make test (all green; never skip or delete a failing test)
- Lint: make lint (zero warnings)

Run all three before reporting any task complete, and paste the output.
If a test fails, fix the code, not the test.
Governance considerations
What is enforced: Verification before a task is reported done, and the block on the agent editing test files during a fix, both implemented as hooks where the organization wants them guaranteed.
What the evidence is: The literal output of make test, the build log, or the screenshot diff that Claude ran and pasted, so the evidence comes from the toolchain.
Where it is logged: In the session transcript, which the OpenTelemetry export forwards to the organization's observability stack, and in the PR's check run, where the reviewer and any later auditor can both see it.
Who approves: The code owner reviewing the PR, who can concentrate on intent and risk because the mechanical evidence is already attached.
How to measure it
Leading indicator: First-pass CI success rate for agent-written changes, which the CI system already supports.
Lagging indicator: Review time per PR (from the PR metadata), which should fall once the tests catch what reviewers used to catch, and the change failure rate from an incident tracker.
Was this helpful?
Previous lesson
Parallel sessions and subagents
Next lesson
Continuous evals in CI
Lesson 8 of 14 · The AI-Native SDLC Playbook
Give Claude a feedback loop
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
