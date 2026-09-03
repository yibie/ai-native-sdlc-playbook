# 阶段 3 · 把 Claude Code 的 plan mode 设为默认起点

AI 原生 SDLC 实践手册
把 Claude Code 的 plan mode 设为默认起点
第 4 课
5 分钟

工程师在 plan mode 下启动 Claude Code 会话，把阶段 2（设计）产出的已批准 spec.md 交给 Claude，让 Claude 对工程师进行访谈式提问，反复迭代方案，直到工程师满意为止。

会有什么改变
传统做法	AI 原生做法
工程师读完设计稿就开始写代码。改动具体怎么做——动哪些文件、跑哪些测试——只存在于工程师的脑子里，最好的情况也只是留在 ticket 的评论里。没有人能审查它。审查者看到的第一样东西就是已经完成的 diff，到那时再返工就慢了。	工作从一份书面方案开始——Claude 在 plan mode 里产出这份方案，此模式下它可以只读代码库而不做任何改动。工程师在代码写出来之前修正方案，批准后的版本提交为 plan.md，供后续阶段对照检查。

开始之前
前置条件：意图产物（intent.md 或 spec.md），如果有的话；有 CLAUDE.md 文件会很有帮助。
基础设施：能访问仓库的 Claude Code。

如何执行
1. 工程师在 plan mode 下启动与 Claude 的会话。
2. 工程师把 intent.md 和 spec.md 交给 Claude，请它产出一份实现方案，方案要指明哪些文件会改动、工作的先后顺序，以及能证明改动成立的测试。
3. 审问这份方案：问这次改动可能破坏什么、哪一步风险最高、Claude 放弃了哪些别的选项。
4. 反复迭代，直到一个从未看过这段对话的工程师，只凭方案就能把改动实现出来。
5. 把批准后的方案提交为 plan.md。方案自此进入审计轨迹，PR 审查这个 play（阶段 5：部署）会拿最终的 diff 与它对账。
6. 接受方案，让 Claude 动手实现。方案足够扎实时，实现往往一遍就能过。
7. 实现偏离方案时，在同一个 commit 里更新 plan.md。可以考虑用 hook 强制两者保持同步。

实际长什么样

plan.md：

markdown

# 计划：claims 状态自助服务（来自 intent.md 2026-06-02）

## Files that change

portal/src/claims/StatusPanel.tsx (new), claims-api/routes/status.py, claims-api/tests/test_status.py

## Order of work

1. Add the status endpoint behind existing auth.
2. Panel against the endpoint.
3. Wire into the portal nav.

## Risks

The claims-core API rate-limits at 50 rps; the panel must cache.

## Proof

test_status.py covers the four claim states; screenshot matches the approved mock.

治理考量

设计评审发生在任何代码生成之前——此时改变方向还只是改一份文档的事。plan mode 本身就强制了这一点：在工程师接受方案之前，Claude 无法编辑文件。方案及其修订过程都会被记录，连同是谁批准了它。常规改动由工程师自己批准，组织认定的高风险改动则要交给技术负责人或架构师。

如何衡量
先行指标：一次实现就合并的改动占比，以及从方案批准到 PR 合并的时间（所需数据放在 PR 元数据里）。
滞后指标：每次改动的返工轮数（同样取自 PR 元数据），以及合并后的 diff 与已提交的 plan.md 仍然相符的频率。

Claude Code 的 auto mode

Claude Code 也可以跑在 auto mode 下：工程师迭代并批准方案，之后 Claude 逐项应用改动而无需每处编辑都征求确认。随着后面几个 play 的护栏逐渐成熟（调校好的 CLAUDE.md、把策略固化成代码的 skill、拦截危险操作的 hook、Claude 能自己跑的测试套件），auto mode 会成为例行工作的默认模式：spec.md 写得很紧、爆炸半径小、代码早已被测试覆盖。

重心就此转移：从用户盯着 agent 做改动、逐个审查动作，转向在更长的自主会话结束后审查产物。结合 worktrees 使用时，auto mode 还能进一步释放个人和团队层面的并行能力，也是自主运行 SDLC、如阶段 6（维护）所述闭环运转的基础。

遗留系统与真相之源

现有 SDLC 流程多半已经在追踪产物了，只是不用 Markdown 文件而已。工作项可能在 Jira 里，需求放在带法规追溯能力的工具里，设计稿在 Figma 中，变更审批要走变更委员会。这些系统很难被替换——审计和监管部门已经认可它们，别的团队也依赖它们——所以 AI 原生 SDLC 必须迁就现状。流程产出的每份产物，都要指定一个系统作为真相之源，其余系统只保留副本或链接。

下面这些配置都能实现"单一真相之源"，每种产物可以各有选择：

把仓库作为真相之源。Markdown 产物是权威记录，遗留系统引用 commit 里的文件。对工程主导的组织来说，这通常是最干净的配置之一：所有记录都在一个工具里，由同一个时间戳权威把关。
以遗留系统为真相。Jira、ServiceNow 或需求管理工具持有权威记录，Markdown 产物只是工作副本。Claude 在会话开始时读取记录，并在产出 spec 或 plan 的同一个会话里，通过 Model Context Protocol (MCP) 连接器把结果写回遗留系统。
以链接为最低标准。所有产物都标注记录 ID，所有遗留记录都包含对应 Markdown 文件的 commit SHA。向 AI 原生 SDLC 迁移时，链接方案是很好的起步点——因为此时事实上有两个真相之源。

只要两者之间存在链接，或者其中一方被明确指定为真相之源，遗留系统和 AI 原生的 Markdown 优先系统就可以共存。

这有帮助吗？
上一课
需求与设计
下一课
CLAUDE.md
第 4 课 / 共 14 课 · AI 原生 SDLC 实践手册
把 Claude Code 的 plan mode 设为默认起点
引言
引言
阶段 1：规划
记录为 intent.md
阶段 2：设计
需求与设计
阶段 3：构建
把 Claude Code 的 plan mode 设为默认起点
CLAUDE.md
把 skill 变成机构知识
并行会话与 subagents
阶段 4：测试
给 Claude 一个反馈回路
在 CI 里持续跑 evals
阶段 5：部署
AI 参与 PR 审查回路
用 hook 充当审批门禁
CI/CD 集成与部署
阶段 6：维护
用指标闭环
结语
结语与参考资料
课程完成
会有什么改变
开始之前
如何执行
实际长什么样
治理考量
如何衡量
Claude Code 的 auto mode
遗留系统与真相之源

---
