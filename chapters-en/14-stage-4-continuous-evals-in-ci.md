# Stage 4 · Continuous evals in CI

The AI-Native SDLC Playbook
Continuous evals in CI
Lesson 9
3 min

Evals are the AI-native equivalent of stage-gate QA. In practice that means a suite that runs whenever the agent's configuration changes. When a new model is swapped in or a prompt is rewritten, the eval suite says whether the agent still does the work to the same standard.

The evals should be seen as a live suite. As models improve, cases that once discriminated stop doing so, and new ones must be added that arise from ongoing monitoring.

Depending on the use case, some teams may prefer to run these evals offline on a set cadence rather than on every change. The steps below are for continuous evaluations.

Getting started
Prerequisites: The CLAUDE.md and feedback loop (Stage 4: Test).
Infrastructure: CI that can run Claude Code non-interactively, and an API key with budget for eval runs.
How to execute it
The platform engineer collects 20 to 50 real tasks from recent work, each with its expected or accepted outcome.
Write each task as an eval, meaning the prompt plus the checks that define acceptable (tests pass, lint clean, behavior unchanged, policy followed).
The suite runs non-interactively in CI on a schedule and on any change to CLAUDE.md, skills, or hooks, since that configuration steers the agent and deserves the regression testing that code gets.
Gate configuration changes on the results. A skill change that drops the pass rate gets reviewed before it merges.
Each production incident gets an eval, written by the team that owned the incident, and stays in the suite as a regression test.
What it looks like

.github/workflows/agent-evals.yml:

yaml
name: Agent evals
on:
  pull_request:
    paths: ['CLAUDE.md', '.claude/**']
  schedule:
    - cron: '0 2 * * *'
jobs:
  evals:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install -g @anthropic-ai/claude-code
      - name: Run eval suite
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          for eval in evals/*.json; do
            claude -p "$(jq -r '.prompt' $eval)" \
              --allowedTools "Read,Edit,Bash(make test)" \
              --output-format json > result.json
            ./evals/check.sh "$eval" result.json
          done
Governance considerations

Evals give QA a gate that keeps up with agent output. The pass-rate threshold is enforced as a merge check, runs are logged so results can be compared over time, and the team that owns the configuration change approves it.

How to measure it
Leading indicator: The eval pass rate over time, reported by the suite on every run, and how long a production incident takes to become a permanent eval.
Lagging indicator: Regressions caught in CI compared with regressions found in production derived from the incident tracker.
Was this helpful?
Previous lesson
Give Claude a feedback loop
Next lesson
AI in the PR review loop
Lesson 9 of 14 · The AI-Native SDLC Playbook
Continuous evals in CI
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
