# Stage 6 · Closing the loop on metrics

The AI-Native SDLC Playbook
Closing the loop on metrics
Lesson 13
6 min

Where every earlier stage needs a person to start it, Stage 6 shifts the focus to autonomous running of Claude to close the loop. For example, a continuously running monitoring agent could, off the back of a bug ticket being raised, create an intent.md, and flow through the requirements, plan, build, test, and review phases. Stage 6: Maintain runs headless, with an independent confidence gate between stages, a deterministic check or an adversarial reviewing agent, deciding whether the previous stage's output continues or is escalated to a human.

What changes
Traditional	AI-native
Maintenance is a reactive phase. All tickets or incidents wait on a person to act on them and restart the process. An alert fires at 3 a.m. and can be missed, a ticket can sit in the backlog until someone picks it up, and post-mortem actions may not reach the codebase at all if another fire starts first.	A trigger such as a control-band breach, a ticket, a channel message, or a schedule invokes Claude without a person in the path. Claude diagnoses, acts only through gated routes, and writes what it finds as intent.md, which then goes through the stages described above. People triage and review that work, and no longer have to start it.

A deterministic script watches production and invokes Claude when a control band is breached. Monitoring of a breach is a helpful example of the pattern for the loop running autonomously, while the Claude Tag (public beta) section at the end of the stage covers work arriving through different channels.

Getting started
Prerequisites: intent.md, which gives the loop a structured output to restart. Claude-accelerated PR reviews, hooks as an action boundary, and a rollback path for CI/CD (which the highest autonomy tier invokes).
Infrastructure: A metrics store the detection script can query (Prometheus, the CI system's API, or equivalents), read access to the repository, a way to run Claude Code non-interactively in CI, or the Agent SDK for a service that receives webhooks.
How to execute it
The service owner or platform engineer picks one metric with a stable rolling baseline, such as CI test failure rate, post-deploy 5xx rate, or PR cycle time.
They write the detection script, typically mean and standard deviation over a rolling window with rules (Western Electric or similar) so the bands catch slow drift as well as spikes. The script is version controlled and unit tested, and detection stays entirely deterministic, with no model involved.
Response tiers are defined in version-controlled config (bands.yaml below). At 1σ the script only logs, at 2σ it invokes Claude read-only to diagnose, and at 3σ Claude may act, though only by opening a PR into the review gate or triggering a pre-approved runbook.
The trigger layer can be a scheduled workflow in GitHub or GitLab, a webhook from the existing monitoring stack, or a cron job inside the network. Claude runs stateless, either as a non-interactive step on a CI runner or as an Agent SDK service in a sandboxed container, and the CI/CD play covers the deployment and model-access options. Because the run is stateless and non-interactive, a loop can begin and end without anyone starting it.
The agent writes its diagnosis as intent.md in the Stage 1: Plan format, covering the anomaly and its evidence, a proposed outcome, the affected systems, and any open questions. From there the finding goes through the pipeline like anything else.
The service owner or on-call engineer triages the queue, routing product-facing findings to the product owner. Fix now, schedule, or dismiss. Dismissals tune the bands and help to reduce noise.
When a fix ships, add an eval for the incident (the continuous evals play) to ensure that such issues are protected against going forwards.
What it looks like

For example, a bands.yaml monitoring CI test failure rate:

yaml
metric: ci_test_failure_rate
baseline: rolling_30d
rules: western_electric
tiers:
  1sigma: { action: log }
  2sigma: { action: diagnose,
            tools: "Read,Grep,Bash(gh run view *)" }
  3sigma: { action: propose,
            routes: [pull_request, runbook:rollback-deploy] }
Governance considerations

The tier boundaries are enforced from version-controlled config, with permissions and managed settings denying production access. Invocations, findings, and triage decisions are logged with a timestamp. A service owner triages and approves findings, resulting changes go through the normal PR review gate, and the runbooks the agent may trigger were approved in advance.

How to measure it
Leading indicator: Time from band breach to an intent.md in the triage queue, against the old time from incident to post-mortem action. The detection script's log has the breach timestamp and tier of incident.
Lagging indicator: The share of findings that become merged fixes (triage queue against actual PR history), and repeat incidents of the same class, which should fall as the fixes add cases to the eval suite.
Examples
When the CI test failure rate breaches 3σ, the agent quarantines the flaky test or opens a revert PR, and the review gate decides.
When the post-deploy 5xx rate breaches 3σ with a deployment in the window, the agent triggers the existing rollback pipeline.
When PR cycle time trips a drift rule, the agent writes a report for engineering leadership, which shows the harness works for process metrics as well as production ones.
Claude on call with Claude Tag

Incidents can also arrive via other means such as workplace communication apps, like Slack or Microsoft Teams. Incidents can look like a 10 p.m. Slack message for an urgent fix on an incident channel and can now be actioned immediately. Claude Tag (public beta currently available in Slack) makes Claude a member of those channels under its own identity, so each new incident gets a first responder and the response itself becomes part of the loop and memory for future incidents.

The conversation and institutional knowledge stay in the channel, with anyone in the channel able to guide and action the response. Any team member can test hypotheses, explore new options, and investigate in real time with the channel history adding to the auditability. Through access to MCP, Claude verifies the metric is back at baseline and confirms it in the thread, and writes the post-mortem to a version-controlled lessons file that future investigations can read.

Incidents are not the only work Claude Tag picks up. Tagged on a ticket over MCP or asked in the channel, Claude triages the work the same way. A small, well-bounded fix arrives as a PR through the review gate, and anything larger is written up as intent.md for Stage 1: Plan, at which point the loop starts feeding itself.

Was this helpful?
Previous lesson
CI/CD integration and deployment
Next lesson
Closing thoughts and resources
Lesson 13 of 14 · The AI-Native SDLC Playbook
Closing the loop on metrics
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
Claude on call with Claude Tag


---
