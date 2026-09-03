# 第 1 阶段 · 将想法记录为 intent.md

AI 原生 SDLC 实战手册
将想法记录为 intent.md
第 2 课
4 分钟

软件开发流程由 intent.md 开启，而 intent.md 可能来自不同的入口：一个人产生了一个想法、有人提交了一张工单，或是一条告警暴露了一次事故（参见第 6 阶段：维护）。

当想法来自某个人时，他会先和 Claude 头脑风暴，产出一份 Markdown 格式的规格草案（proto-spec）。在传统 SDLC 中，同一个人接下来还得去说服产品团队中的某位成员，让对方和他一起——或替他——把这个想法正式写下来。

Claude 产出的这份规格草案人类可读、受版本控制，下一阶段拿过来就能直接用。这份规格草案最终保存为 intent.md。

无论意图来自事件触发还是某个人，后续步骤都是一样的：agent 写出的 intent.md，在提交之前都要经过产品负责人（product owner）的审阅与修正。

有什么变化

| 传统 | AI 原生 |
| --- | --- |
| 一个想法要先流经积压条目（backlog）、用户故事、故事点估算和细化会议，才会有人动手做。每一次交接都发生一次所有权转移，等它传到工程团队手里时，已经和提出者最初的意思隔了好几层。 | 提出者直接和 Claude 头脑风暴，用自己的话把结果写进 intent.md，形成一份规格草案。这份产物写清楚了想要什么、为什么想要、在哪些约束下做；可重复的流程则编码成 skill。 |

准备工作

前置条件：无。

基础设施：为非工程师提供 Claude 访问入口（claude.ai 或 Cowork）；一份约定好的 intent.md 模板；一个共享的、受版本控制的 intent 存放地，由产品负责人照看。产品线单一时，最简单的存放地就是产品仓库里的 intent/ 目录——这样，文档链就和由它衍生出的代码待在了一起。只有当 intent 横跨多个仓库时，单独建一个 intent 仓库才值得那份额外开销；在 monorepo 中，它只是一个目录。这个存放地与已经承担记录职责的 Jira 或需求工具如何配合，见第 3 阶段：构建中的「遗留系统」一节。

这套基础设施由平台团队或工程团队一次性搭建。由于贡献者会来自组织各处，需要一名技术人员先把 intent 存放地建起来，并决定谁有写入权限。

仓库就绪之后，没有 Git 经验的贡献者不必直接使用 Git。只要接入一个连接器（例如连到 GitHub 这类版本控制系统），Claude 就能从 claude.ai 或 Cowork 里替他们把 Markdown 文件提交上去。

如何执行

提出者用自己的话向 Claude 描述问题。可以说说现在做不到什么、这个想法会影响到谁、更好的状态应该是什么样、哪些内容不在范围内。不需要使用任何正式的语言。

继续头脑风暴，直到想法变得具体。Claude 会问出分析师会问的那些问题：范围、用户、约束条件，以及怎样才算成功。

请 Claude 用组织的模板把结果写成 intent.md。该模板可以编码成一个 skill——由技术人员搭建、负责人签字确认。模板一般覆盖：问题、预期成果、受影响的用户与系统、约束条件，以及待决问题（open questions）。

提出者把 Claude 理解错的地方逐一改正。

把 intent.md 提交到共享存放地。作者与时间戳就此进入记录，产品负责人从这里接手这个想法。

效果示例

intent.md：

```markdown
# 意图：claims 状态自助服务
Author: J. Ortiz (claims operations). Status: draft.
## Problem
Customers phone the contact center to ask where their claim is.
Handlers spend roughly a third of call time on status-only queries.
## Proposed outcome
Customers see claim status, next step and expected date in the portal.
## Affected users and systems
Claims handlers, portal team, claims-core API.
## Constraints
No new PII in the portal session. Existing authentication only.
## Open questions
Do third-party loss adjusters need access too?
```

治理考量

治理依据就是已提交的 intent.md：作者、时间戳、完整的修订历史都在里面，并记录在 intent 存放地的 Git 历史中。产品负责人负责批准——这份 intent 是被接受并流入第 2 阶段：设计，还是被驳回，以产物被合并（merge）或被关闭评审（closing review）的形式留下记录。

如何度量

领先指标：从第一次对话到 intent.md 完成提交所花的时间。直接从 intent 存放地的 Git 历史读取即可，那里记录了作者与时间戳。预期这一时间会从过去数周的「需求挖掘与细化」周期，缩短到几小时。

滞后指标：存活率，即被产品负责人接受、进入第 2 阶段：设计（而不是被关闭）的 intent.md 所占比例。接受或拒绝的决定，以产物被合并或评审被关闭来记录。另外，还可以统计同一变更在 spec.md 首次提交之后，intent.md 又被改动了多少次。

这篇内容有帮助吗？

上一课
引言
下一课
需求与设计

第 2 课（共 14 课）· AI 原生 SDLC 实战手册
将想法记录为 intent.md
引言
引言
第 1 阶段：计划
将想法记录为 intent.md
第 2 阶段：设计
需求与设计
第 3 阶段：构建
默认以 Claude Code 的 plan 模式为起点
CLAUDE.md
把 skill 沉淀为组织知识
并行会话与 subagents
第 4 阶段：测试
给 Claude 一个反馈回路
在 CI 中持续跑 evals
第 5 阶段：部署
让 AI 参与 PR 评审回路
用 hooks 充当审批闸门
CI/CD 集成与部署
第 6 阶段：维护
用指标闭合回路
结语
结语与资源
课程完成

有什么变化
准备工作
如何执行
效果示例
治理考量
如何度量

---
