# 第 5 阶段 · AI 参与 PR 评审

AI-Native SDLC 实践手册
AI 参与 PR 评审
第 10 课
4 分钟

Claude 既给出评审，也接受评审。它对照组织的策略评审收到的 PR，也在自己提交的 PR 上回应评审意见。这让工程师在 PR 评审中可以专注于行为层面——说到底就是判断意图与风险。

有什么变化

传统	AI 原生
评审产能围绕人力的产出规划。一个 PR 要等评审者通读全部内容，评审质量随评审者当时的负荷起伏，作者在后面追着催，积压却越堆越高。	所有 PR 都接受同一套评审流程，结论按严重程度分级。人力的注意力上移到更高层面——这个改动是否实现了计划中的意图、风险是否可接受。

开始之前
先决条件：第 3 阶段（Build）产出的、已更新的 CLAUDE.md 文件；如果评审流程要强制执行书面策略，还需要 skills 和定义好的 subagents。
基础设施：一个安装了 Claude 集成的仓库——要么由管理员启用受管的 Code Review（research preview）服务，要么在自家 CI 里跑 claude-code-action，需要时可通过 Amazon Bedrock、Google Cloud 的 Vertex AI 或 Microsoft Foundry 调用模型（部署选项在 CI/CD 篇里有介绍）。要求 code owner 批准的分支保护策略也值得配上。

如何执行
受管的 Code Review 服务是最快的起步方式。管理员启用它并选择仓库。当需要掌控流水线，或希望 API 调用走自家云协议时，就在自己的 CI 中用 claude-code-action 跑评审（相关管道细节见 CI/CD 篇）。
技术负责人把评审策略写成仓库根目录下的 REVIEW.md，按组织在意的各个关卡划分：bug 与逻辑错误；安全与漏洞；对照 spec（需求篇中的 spec.md）、实现计划（plan 模式篇中的 plan.md）和设计原则的合规性。REVIEW.md 还定义什么是 Important、什么只是 Nit，以及哪些内容要跳过。
技术负责人设定人工介入的阈值。评审结论本身既不通过也不阻止一个 PR，分支保护仍然要求 code owner 的批准。平台工程师若想按评审结论把关合并，可以读取该检查运行以机器可读计数发布的严重程度统计。
当评审者或作者在某条评审意见上 @claude 时，Claude 会回应这条意见并推送修复。PR 线程中同时记录请求与改动。这个修复循环由 claude-code-action 驱动。在受管服务中，评论 @claude review 则会触发一次全新的评审。对 Claude 自己开的 PR，可以更进一步——让 Claude 全程照看 PR 直到合并。团队会把这个循环包进自定义 slash command：扫清 PR 上未解决的评审意见和失败的检查，逐一处理并推送修复，直到 PR 变绿、只等 code owner 批准。
评审结论会回写给 CLAUDE.md。当评审第二次标记同一类错误时，纠正内容就作为该次评审的一部分写进 CLAUDE.md；由于评审会读 CLAUDE.md，从下一个 PR 起这个错误就会被拦下。评审还会在某个改动让 CLAUDE.md 过时的时候发出提示。
技术负责人每月调一次配置：给结论打分让评审者持续改进，并在 REVIEW.md 中设 Nit 数量上限。生成路径（generated paths）和 CI 已强制检查的内容不计入。

实际效果

REVIEW.md:

markdown

# 评审指令
## Passes
Run three passes and tag each finding with its pass:
- Bugs: logic errors, broken edge cases, subtle regressions
- Security: injection risks, authentication gaps, PII in logs
- Compliance: the change matches spec.md, plan.md and our design principles
## What Important means here
Reserve Important for findings that would break behavior, leak data
or breach a policy. Style and naming are nits.
## Cap the nits
Report at most five nits per review; summarize the rest as a count.
## Do not report
Generated files under src/gen/ and anything CI already enforces.

治理考量

职责分离得到保留，因为写代码的 agent 没有途径批准自己的代码。REVIEW.md 中的评审策略应用于所有 PR，结论、修复、评分与批准都记录在 PR 历史中，因此 PR 本身就是审计记录。批准来自走分支保护的人工——在结论的支撑下做出决定。

如何衡量
先行指标：首次评审时间——应缩短到分钟级；以及不需要人工碰分支就解决掉的评审意见占比，数据直接存在 Git 上。
滞后指标：合并前拦截到的缺陷与漏洞，对照漏到生产环境的那些——数据来自 PR 历史和事故追踪器。

这篇有帮助吗？
上一课
CI 中的持续 evals
下一课
作为审批闸门的 hooks
第 10 课 / 共 14 课 · The AI-Native SDLC Playbook
AI 参与 PR 评审
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
有什么变化
开始之前
如何执行
实际效果
治理考量
如何衡量

---
