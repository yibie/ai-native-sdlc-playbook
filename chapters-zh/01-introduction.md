# 引言

AI 原生 SDLC Playbook
引言
第 1 课
6 分钟

组织已经开始用 AI 以一年前无法想象的速度写代码，但围绕代码的流程却没有以同样的速度跟上。

许多工程团队仍然沿用同样的审批关卡、评审、交接和策略，拖慢了通过 Claude Code 这类 agentic 编码方案获得的效率提升。

传统 SDLC

软件开发生命周期（SDLC）是把软件从想法带到生产环境的过程。大多数组织都在运行同一套六个阶段的某种变体，涵盖软件的规划、设计、构建、测试、部署和维护。传统上，每个阶段都是一段独立的流程，由不同的角色负责。产品经理写需求，技术架构师把需求变成设计，工程师实现设计，受监管企业的 QA 团队验证软件，发布团队负责发布，运维团队监控线上运行的东西。工作通过文档、工单和签字在各个阶段之间流转。

传统的软件开发生命周期（SDLC）流程繁重，是为了在每一步都确保责任明确、可控。然而，传统 SDLC 设计的初衷是在写代码、实现代码最耗时最昂贵的时代最大化效率，而如今情况已经变了。产品需求文档（PRD）、估时仪式、产品安全评审的存在，都是为了在可能持续数周、数月乃至数季度的开发工作中强制各方对齐。

传统 SDLC 还内置了许多控制手段，其前提是每一步都由人来执行。而如今创造最大价值的组织，已经围绕 agentic AI 现在能做到的事情重建了流程，同时确保人始终在环内（human in the loop）。在本指南中，我们会介绍我们 Applied AI 团队将 Claude 内部集成到 SDLC 各个阶段的最佳实践——这些实践源于我们与客户合作的经验——以加速开发、让流程跑得更快。

当代码不再是瓶颈

当代码不再是瓶颈、构建阶段跑得比传统 SDLC 允许的速度更快时，三件事会成真：

瓶颈转移到构建阶段两侧的阶段：主要是规划、评审/测试和部署，这些阶段仍然以人的速度运行。

控制手段不再匹配现实，变得难以执行。当 diff 是一个人写的时候，逐行人工评审说得通；可一旦大部分 diff 是 agent 写的，这种方式就跟不上了。

治理成本上升，因为例外情况仍然要通过每周或每月才开一次会的委员会和会议来流转。

瓶颈转移了：构建塌缩到 agent 的速度，而它两侧的阶段仍然以人的速度运行。

拿安全瓶颈举例。安全团队是按人类产出规模配置的，所以当 agent 把代码产出放大时，要么评审队列越积越长，要么代码在评审不足的情况下就发布了。受监管的组织两种结果都无法接受，所以它的安全和策略检查必须跟上 agent 的速度。

为了更好地兑现 agentic AI 的效率收益并确保其安全，传统 SDLC 生命周期需要经历与实现阶段同等程度的变革。

什么是 AI 原生 SDLC？

AI 原生 SDLC 是一个重新构想过的流程：保留旧的控制目标，换上新的执行方式。它不再是线性流，而是变成一个环，AI 嵌入到每一个节点。AI 原生 SDLC 推动自动化交接、自动触发后续 plays，从而解决传统 SDLC 各阶段之间靠人工交接、笨拙生硬的问题。

左侧是传统线性 SDLC，右侧是 AI 原生持续循环；人位于循环之上，负责发起、指挥和治理。

转变在哪里

下表标出了传统 SDLC 与由 Claude 支撑的 AI 原生 SDLC 之间这个光谱的两端。大多数组织都处在这两列之间的某个位置。

阶段	传统 SDLC	AI 原生 SDLC
Plan（规划）	需求由委员会收集，经过工作坊和签字提炼，再手工撰写	Claude 直接从源头综合痛点，并写进 intent.md——人类可读、机器可执行
Design（设计）	规格由分析师撰写，设计师再解析	需求与设计压缩进一次由 agent 主导的工作会话，由编码为 skills 的标准引导，并在 Git 中做版本管理
Build（构建）	测试和代码手写，文档在主开发完成之后才补	测试和代码由 AI 生成，机构知识以带版本管理的机器可读 CLAUDE.md 文件和 skills 维护
Test（测试）	在阶段边界设 QA 关卡	持续的 evals 贯穿实现过程
Deploy（部署）	人类评审每一行代码，治理发生在评审周期中，而且往往并不一致	多层 agentic 评审，人工评审只保留给受监管和关键代码。治理在 AI 行动的同时即被强制实施，以 hooks 作为审批关卡
Maintain（维护）	人盯着生产环境找 bug	Agent 监控线上部署。任何被突破的控制带都会被诊断，并作为新的 intent.md 写回循环

把 AI 原生这一列串起来的，是已提交的产物（artifact）。每个阶段结束都把一个产物写入版本控制（包括 intent.md、spec.md、plan.md、diff 及其测试、带评审结论的 PR、事故记录），下一个阶段从读取它开始。在早期阶段，.md 文件是主要产物，因为产品负责人和 agent 都能读同一份文件并对它采取行动。从 Build 往后，产物是代码及其记录。提交链本身就是审计轨迹：谁要求了什么、agent 产出了什么、谁批准了它。

每一个需要判断力的决定，最终仍由人负责。在 agentic SDLC 的世界里，人的注意力随着需要评审的产物一起转移。

plays 如何运作

plays 是本 playbook 的核心，按六个非线性阶段（Plan、Design、Build、Test、Deploy、Maintain）分组，共同覆盖完整的生命周期。

每个 play 覆盖：

改变了什么
如何开始
落地的具体步骤
治理考量
如何衡量它是否有效

plays 是模块化的，组织可以根据自己的独特需求，在不同的时间优先改造不同的阶段。每个 play 都在 "Prerequisites" 下列出它的依赖，依赖图对此有更直观的呈现。

一个阶段以提交产物结束，这次提交随即启动下一个阶段。被接受的 intent.md 触发需求与设计流程，被批准的 spec.md 触发 plan mode，合并的 PR 触发流水线，生产环境中被突破的控制带写下下一个 intent.md——循环就这样继续下去。

一开始，每个步骤都要你手工提示；最终状态是一个循环：每个被接受的产物都会触发下一道关卡。人的注意力集中在关卡处——评审 agent 标记出来的内容，而不是每个阶段都从零开始。

plays 依赖图。最上面一行的 plays 没有前置依赖；实线箭头指向建立在它之上的 plays，虚线箭头表示有助益但不是必需。

这篇有帮助吗？
下一课
Capture as intent.md
14 课中的第 1 课 · The AI-Native SDLC Playbook
引言
引言
引言
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
传统 SDLC
当代码不再是瓶颈
什么是 AI 原生 SDLC？
转变在哪里
plays 如何运作
