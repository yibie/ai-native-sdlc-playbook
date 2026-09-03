# Stage 5 · Hooks as approval gates

The AI-Native SDLC Playbook
Hooks as approval gates
Lesson 11
5 min

The build phase used hooks as guardrails, allowing or blocking actions with no human involved (Stage 3: Build). A hook can also ask, pausing the action until a specific person approves, which is what release gating needs.

The play sits in Stage 5: Deploy because the release gate is the clearest case, but hooks are not deploy-specific: they run wherever Claude acts. For example, hooks can block edits to migrations and infra without a change ticket during Stage 3: Build, and stop the agent editing test files during a fix task in Stage 4: Test.

Getting started
Prerequisites: None.
Infrastructure: A written list of the approvals the change process requires.
How to execute it
Engineering leadership, with change management and compliance, lists the human approval gates that must survive, such as change management sign-off, release authorization, and edits to protected paths.
The platform engineer expresses each gate as a hook, a script that runs before Claude acts that can allow, ask, or block.
Team hooks go in .claude/settings.json in Git, and non-negotiable hooks go in managed settings owned by the platform or IT admin, where individual engineers cannot switch them off.
A block should explain itself, so when a hook stops an action, the reason and the route to approval appear in Claude's output.
What it looks like

A standalone example in the project's .claude/settings.json:

json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/production-gate.sh" }
        ]
      }
    ]
  }
}

And the gate itself (.claude/hooks/production-gate.sh):

bash
#!/bin/bash