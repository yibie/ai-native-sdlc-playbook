# 第 5 阶段 · 作为审批闸门的 hooks

AI-Native SDLC 实践手册
作为审批闸门的 hooks
第 11 课
5 分钟

构建阶段把 hooks 用作护栏：没有人参与，直接放行或拦截动作（第 3 阶段：构建）。hook 也可以选择询问：把动作暂停，直到某个具体的人批准——这正是发布闸门需要的形态。

这个做法放在第 5 阶段（Deploy），是因为发布闸门是最清晰的使用场景，但 hooks 并不专属于部署：只要 Claude 在行动，它们就会运行。例如，在第 3 阶段（Build）期间，hooks 可以拦截没有变更单就修改迁移脚本和基础设施（infra）文件的行为；在第 4 阶段（Test），当修复任务进行中，hooks 能阻止 agent 改动测试文件。

开始之前
先决条件：无。
基础设施：一份书面清单，列出变更流程要求的所有审批项。

如何执行
工程负责人与变更管理、合规团队一起，把必须保留的人工审批关卡列出来——例如变更管理签核、发布授权，以及对受保护路径的修改。
平台工程师把每个关卡表达成一个 hook：一个在 Claude 行动前运行的脚本，可以给出三种结果之一——放行（allow）、询问（ask）或拦截（block）。
团队的 hooks 放进 Git 里的 .claude/settings.json；不容妥协的 hooks 放进由平台或 IT 管理员持有的受管设置中，个别工程师无法把它们关掉。
拦截应当能自我解释：当 hook 停下某个动作时，原因和获得批准的途径都要出现在 Claude 的输出里。

实际效果

项目 .claude/settings.json 里一个独立的示例：

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

而闸门脚本本身（.claude/hooks/production-gate.sh）：

bash
#!/bin/bash

# 生产部署需要具名的发布授权
cmd=$(jq -r '.tool_input.command' < /dev/stdin)
if [[ "$cmd" == *"deploy"* && "$cmd" == *"production"* ]]; then
  if [ -z "$RELEASE_APPROVAL" ]; then
    echo "Production deploys need a release authorization." >&2
    exit 2   # exit 2 blocks the action; the message goes to Claude
  fi
fi
exit 0

治理考量

hooks 就是审批闸门。闸门条件每次执行、对每个人都强制执行。每一次放行与拦截的裁决都连同时间戳记入日志。闸门还定义了什么算作批准——是一张已批准的变更单，还是发布经理的签核。

受监管企业的受管设置

面向受监管企业的受管设置，由平台团队通过移动设备管理（MDM）或管理控制台下发。工程师无法编辑或覆盖其中的任何设置。见下：

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

这些设置在控制层面做什么：

permissions.deny 让密钥进不了 agent 的上下文，并拦截通过工具发生的任意网络外联。permissions.allow 预先批准安全的内循环操作，这样 deny 清单不会变成提示疲劳（prompt fatigue）。
disableBypassPermissionsMode 加上 allowManagedPermissionRulesOnly，意味着没有任何工程师、项目文件或命令行参数能扩大这些规则的适用范围。
sandbox 补上 permissions 管不到的部分。对 WebFetch 做工具级的 deny，拦不住 shell 命令触网；而操作系统级的域名白名单则把外联彻底挡死——两者在不同层次上执行同一个目标。failIfUnavailable 和 allowUnsandboxedCommands 把沙箱变成一项前置条件：沙箱无法初始化时 Claude Code 拒绝启动；在沙箱里失败的命令，也不能拿到沙箱外重试。
credentials 处理的是 deny 规则漏掉的场景。permissions.deny 约束的是 Claude 的文件工具，但默认情况下，沙箱中的 shell 命令仍然可能读到 ~/.ssh 或 ~/.aws/credentials。这个块会拒绝这类读取，并把列出的密钥从沙箱命令的环境中剥离。
allowManagedHooksOnly 意味着只有受管设置里定义的 hooks 会运行；用户、项目和本地设置里的 hooks 都会被拦下，包括上文 .claude/settings.json 的独立示例。要让这堂课的审批闸门持续生效，就把它定义在受管文件自己的 hooks 块中。
disableSideloadFlags 和 strictKnownMarketplaces 意味着，工程师机器上的任何 skill、agent、hook 或 MCP server 都来自组织批准的插件市场，而不是某个主目录。市场白名单控制哪些东西可以安装；而那种为了单次运行而旁路加载插件、agent 或 MCP 配置的参数，会在启动时被拒绝。
allowManagedMcpServersOnly 把 agent 的工具面变成一份由平台团队持有的白名单。
requiredMinimumVersion 让低于批准底线版本的 Claude Code 拒绝启动——也就是说，这些控制措施由组织实际评估过的构建版本来执行。

把这个示例当作起点，按你自己的环境去定制。每一条 deny 规则都在拿走一部分能力，平衡点取决于仓库的数据分级。设置参考文档（settings reference）记录了所有键，包括仅限受管的那些。

如何衡量
就 hooks 本身而言：

- 先行指标：在每个审批闸门上等待的时间。每次 hook 裁决都会连同时间戳和放行或拦截的结论写入 OpenTelemetry 导出，因此每个闸门的等待都看得见。
- 滞后指标：hooks 上线前后，闸门违规漏到生产环境的次数——数据来自事故追踪器。

这篇有帮助吗？
上一课
AI 参与 PR 评审
下一课
CI/CD 集成与部署
第 11 课 / 共 14 课 · The AI-Native SDLC Playbook
作为审批闸门的 hooks
引言
引言
第 1 阶段：计划
用 intent.md 捕捉需求
第 2 阶段：设计
需求与设计
第 3 阶段：构建
Claude Code plan 模式作为默认起点
CLAUDE.md
作为机构知识的 skills
并行会话与 subagents
第 4 阶段：测试
给 Claude 一个反馈回路
CI 中的持续 evals
第 5 阶段：部署
AI 参与 PR 评审
作为审批闸门的 hooks
CI/CD 集成与部署
第 6 阶段：维护
闭环于指标
结语
结语与资源
课程完成
开始之前
如何执行
实际效果
治理考量
受监管企业的受管设置
如何衡量

---
