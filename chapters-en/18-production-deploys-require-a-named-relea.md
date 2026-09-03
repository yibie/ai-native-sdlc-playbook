# Production deploys require a named release authorization
cmd=$(jq -r '.tool_input.command' < /dev/stdin)
if [[ "$cmd" == *"deploy"* && "$cmd" == *"production"* ]]; then
  if [ -z "$RELEASE_APPROVAL" ]; then
    echo "Production deploys need a release authorization." >&2
    exit 2   # exit 2 blocks the action; the message goes to Claude
  fi
fi
exit 0
Governance considerations

Hooks are the approval gates. The gate condition is enforced every time, for everyone. Allow and block decisions are logged with a timestamp. The gate also defines what counts as approval, whether that's an approved change ticket or the release manager's sign-off.

Managed settings for a regulated enterprise

Managed settings for a regulated enterprise, deployed by the platform team via mobile device management (MDM) or the admin console. Engineers cannot edit or override any of the settings therein. See below:

json
{
  "permissions": {
    "deny": [
      "Read(.env*)", "Read(./secrets/**)",
      "WebFetch", "Bash(curl *)", "Bash(wget *)"
    ],
    "allow": [
      "Bash(git *)", "Bash(make build)",
      "Bash(make test)", "Bash(make lint)"
    ],
    "disableBypassPermissionsMode": "disable"
  },
  "allowManagedPermissionRulesOnly": true,
  "sandbox": {
    "enabled": true,
    "failIfUnavailable": true,
    "allowUnsandboxedCommands": false,
    "network": { "allowedDomains": ["git.internal.example.com", "registry.npmjs.org"] },
    "credentials": {
      "files": [
        { "path": "~/.ssh", "mode": "deny" },
        { "path": "~/.aws/credentials", "mode": "deny" }
      ],
      "envVars": [ { "name": "GITHUB_TOKEN", "mode": "deny" } ]
    }
  },
  "allowManagedHooksOnly": true,
  "disableSideloadFlags": true,
  "allowManagedMcpServersOnly": true,
  "strictKnownMarketplaces": [
    { "source": "github", "repo": "example-corp/approved-plugins" }
  ],
  "requiredMinimumVersion": "2.1.193"
}

What the settings do, in control terms:

permissions.deny keeps secrets out of the agent's context and blocks arbitrary network egress through tools. permissions.allow pre-approves the safe inner loop so the deny list doesn't turn into prompt fatigue.
disableBypassPermissionsMode plus allowManagedPermissionRulesOnly means no engineer, project file, or command-line flag can widen the rules.
sandbox covers what permissions cannot. A tool-level deny on WebFetch doesn't stop a shell command reaching the network, whereas the OS-level domain allowlist blocks egress outright, so the two enforce one objective at different layers. failIfUnavailable and allowUnsandboxedCommands turn the sandbox into a precondition, meaning Claude Code refuses to start when the sandbox cannot initialize and a command that fails inside the sandbox cannot be retried outside it.
credentials handles a case the deny rules miss. permissions.deny governs Claude's file tools, but a sandboxed shell command could still read ~/.ssh or ~/.aws/credentials by default. This block denies those reads and strips the listed secrets from the environment of sandboxed commands.
allowManagedHooksOnly means only hooks defined in managed settings run; hooks in user, project, and local settings are blocked, including the standalone .claude/settings.json example above. To keep this play's approval gate enforced, define it in the managed file's own hooks block.
disableSideloadFlags and strictKnownMarketplaces mean that any skill, agent, hook, or MCP server on an engineer's machine came through the organization's approved plugin marketplace and not from a home directory. The marketplace allowlist controls what can be installed, and the flags that would sideload a plugin, agent, or MCP config for a single run are rejected at startup.
allowManagedMcpServersOnly makes the agent's tool surface an allowlist owned by the platform team.
requiredMinimumVersion refuses to start on a version below the approved floor, so the controls are enforced by a build the organization has actually assessed.

Treat the example as a starting point to customize to your own environment. Each deny rule removes some capability, and the right balance depends on the data classification of the repo. The settings reference documents all keys, including the managed-only ones.

How to measure it

For the hooks themselves:

Leading indicator: Time spent waiting on each approval gate. Every hook decision is written to the OpenTelemetry export with a timestamp and an allow or block verdict, so the wait is visible per gate.
Lagging indicator: Gate violations reaching production before and after hooks, from the incident tracker.
Was this helpful?
Previous lesson
AI in the PR review loop
Next lesson
CI/CD integration and deployment
Lesson 11 of 14 · The AI-Native SDLC Playbook
Hooks as approval gates
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
Managed settings for a regulated enterprise
How to measure it


---
