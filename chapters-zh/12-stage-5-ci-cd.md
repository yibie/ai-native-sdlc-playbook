# 第 5 阶段 · CI/CD 集成与部署

The AI-Native SDLC Playbook
CI/CD 集成与部署
第 12 课
4 分钟

在 CI/CD 流水线中以非交互方式运行 Claude Code，对执行过程做沙箱化处理，让长时运行的 agent 安全执行；通过 MCP 集成暴露部署能力；在 agent 真正需要回滚之前，先演练好回滚路径。

## 变化在哪里

传统	AI 原生
流水线运行确定性脚本，一切需要判断的事都等着人来处理：比如给 flaky 测试做分类、写 changelog、或者排查构建为什么挂掉。部署和回滚是人在压力之下照着执行的 runbook。	Claude 以非交互方式运行在流水线内部，处理那些需要判断的步骤，运行在带限定凭据的沙箱里。部署工具通过 MCP 暴露给 agent，于是那个写了变更、测了变更的工作流，也能在组织按环境定义的闸门之内，把变更发布出去并回滚。

## 开始之前

前置条件：AI 参与 PR 评审，以及 hooks 作为审批闸门——因为闸门必须先存在，自动化才能加速任何东西通过它们。

基础设施：装好 claude-code-action 的 CI 平台，或任何能调用 claude -p 的 runner；通过 API 获取模型访问，如果流量必须留在组织的云协议范围内，则走 Amazon Bedrock、Microsoft Foundry 或 Vertex AI；为部署目标准备 MCP servers；为 agent 任务准备沙箱 profile，不持有常驻生产凭据。

## 如何执行

平台工程师从只读的判断步骤开始。在流水线任务里用 claude -p 给失败的构建做分类、总结 flaky 测试，或起草 changelog。

在既有闸门之后添加写入步骤，处理修 lint、更新自动生成的文档、或通过 @claude 提及回应评审意见之类的任务。agent 写出的任何东西都经由分支保护以 PR 形式出现，agent 没有任何直接推到 main 的路径。

执行是沙箱化的。agent 任务在容器里运行，受网络策略约束，使用短时有效的限定 token，默认不持有生产凭据。

通过 MCP 暴露部署能力。Deploy、status 和 rollback 变成按环境限定的工具，于是 agent 的部署权限是一个 allowlist，而不是一份带凭据的 shell 脚本。

按环境分级授权。在开发环境，agent 可以自由部署。在生产环境，agent 准备发布、由发布经理批准，用 hook 强制生产闸门。staging 介于两者之间。

回滚应该是流水线里演练最多的路径：一条 agent 能执行的命令，并且定期在 staging 里实际跑一遍。闭环玩法（第 6 阶段：维护）在控制带被突破时会调用这条回滚，所以它必须提前被验证过。

## 实际效果

流水线步骤：

yaml
- name: Triage failed build
  if: failure()
  run: >
    claude -p "Read the build log at out/build.log. Identify the most
    likely cause, say whether the failure looks flaky or real, and write a
    three-line summary for the PR thread." >> triage.md

## 治理考量

治理原则是：agent 可以一直行动到生产闸门之前，但不能越过它。下面的控制手段落实这一原则。

分支保护把 agent 写出的任何东西都变成 PR，没有直达 main 的路径。
生产部署 hook 会拦住发布，直到具名的发布经理批准为止。每次非交互运行都以 agent 自己的身份行动，所以流水线日志能把 agent 做的事和触发它的工程师做的事区分开。
按环境划分的权限层级决定 agent 在通往闸门的路上有多大行动空间。

## 如何衡量

先行指标：不需要呼叫人就能完成分类的流水线故障占比，数据取自 CI/CD 流水线日志。
滞后指标：DevOps Research and Assessment（DORA）指标，CI 系统和部署工具本来就会输出这些数据。

这篇有帮助吗？
上一课
Hooks 作为审批闸门
下一课
用指标闭环

第 12 课，共 14 课 · The AI-Native SDLC Playbook

CI/CD 集成与部署
简介
简介
第 1 阶段：规划
把意图写进 intent.md
第 2 阶段：设计
需求与设计
第 3 阶段：构建
Claude Code plan mode 作为默认起点
CLAUDE.md
Skills 作为机构知识
并行会话与 subagents
第 4 阶段：测试
给 Claude 一个反馈回路
CI 中的持续 evals
第 5 阶段：部署
AI 进入 PR 评审回路
Hooks 作为审批闸门
CI/CD 集成与部署
第 6 阶段：维护
用指标闭环
收尾
收尾思考与资源
课程完成

变化在哪里
开始之前
如何执行
实际效果
治理考量
如何衡量

---
