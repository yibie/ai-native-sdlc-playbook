# 阶段 3 · 把 skill 变成机构知识

AI 原生 SDLC 实践手册
把 skill 变成机构知识
第 6 课
4 分钟

Skill 是组织把机构知识变成可执行能力的方式：规则被明确写下、纳入版本控制、被广泛套用，政策变化时集中更新。经验法则：机构知识中必须被一致执行的部分，写成 skill；本属于 CLAUDE.md 或 prompt 的内容，不要写成 skill。

开始之前
前置条件：无。手头有 CLAUDE.md 会更好——它把 agent 的工作知识留在仓库里——但 skill 并不依赖它。
基础设施：一条有明确负责人的政策，外加一份书面形式的真相之源。

如何执行
挑一条目前执行得并不一致的规则——可以是安全标准、API 设计约定，也可以是品牌规范。
把它写成 skill：一个目录，内含一份 SKILL.md，frontmatter 声明它何时触发，正文写明要做什么。工程师以政策负责人的真相之源为底稿，请 Claude 帮忙写成。
把 skill 放进仓库的 .claude/skills/<name>/，随代码一起发布；也可以做成插件在组织内分发。
测试 skill 确实会触发：换几种不同说法让 Claude 做相关任务，每次都确认 skill 被加载。
政策变更时，同步更新 skill，并让政策负责人签字确认这次改动。
工程师在下一轮会话里自动拿到新版本。

实际长什么样

.claude/skills/secure-api-review/SKILL.md：

markdown
---
name: secure-api-review
description: Apply the API security standard. Use whenever creating or
  modifying an external-facing endpoint, reviewing API code, or
  generating an OpenAPI spec.
---

# 安全 API 评审
When you create or change an API endpoint:
1. Authentication: every endpoint requires the gateway JWT;
   no anonymous routes outside /health.
2. Input validation: validate request bodies against the OpenAPI
   schema and reject unknown fields.
3. Audit: every state-changing endpoint emits an audit event with
   actor, action, entity and timestamp.
4. Data classification: fields tagged pii in the schema must never
   appear in logs or error messages.
Run scripts/check-endpoints.sh and include its output in your summary.

治理考量

Skill 是一种控制手段，不过属于建议性质。它让 Claude 大概率在写代码的同时套用政策，但没有任何机制强制某次会话照办。必须无条件成立的政策，需要在 skill 背后再加一层确定性的东西——比如拦下动作的 hook，或者在 PR 上重新核对政策的审查环节。skill 让违规变得罕见，hook 让违规几乎不可能发生。skill 的每次调用都会记进会话轨迹，政策负责人要像审代码一样审 skill 的改动。

如何衡量
先行指标：从政策负责人批准政策变更，到更新后的 skill 合并，所用的时间——取 skill 目录对应 PR 的时间。
滞后指标：PR 审查中引用该政策的意见条数。skill 开始边写代码边套用政策后，这条数应当趋近于零。如果迟迟不降，要么 skill 没被触发，要么它的文字已经偏离了官方政策。

hook：构建期的护栏

Skill 是建议性控制，hook 是它背后那层确定性的东西。Claude 在实现阶段的大多数动作都是文件编辑和 shell 命令，所以构建期正是 hook 最容易触发的地方。

构建期 hook 可以：

拦截对受保护路径的编辑，比如生成出来的类或已冻结的包
文件编辑后自动跑 formatter 和 linter，让偏差永远攒不下来
把密钥挡在 diff 之外
支撑任何必须毫无例外成立的政策型 skill

hook 会在每个匹配它的动作上运行，所以构建期 hook 必须够快，并且只盯被改动的那个文件。更重的检查——比如整套测试套件——应该放到 commit 或 PR 阶段。

需要向真人征求批准的 hook，归阶段 5（部署）的门禁管：构建期弹出批准请求，等于把真人重新放回所有并行会话的关键路径上。

这有帮助吗？
上一课
CLAUDE.md
下一课
并行会话与 subagents
第 6 课 / 共 14 课 · AI 原生 SDLC 实践手册
把 skill 变成机构知识
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
开始之前
如何执行
实际长什么样
治理考量
如何衡量
hook：构建期的护栏


---
