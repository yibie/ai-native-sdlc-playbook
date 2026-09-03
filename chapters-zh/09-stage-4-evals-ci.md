# 阶段 4 · 在 CI 里持续跑 evals

AI 原生 SDLC 实践手册
在 CI 里持续跑 evals
第 9 课
3 分钟

Evals 就是 AI 原生世界的阶段闸门式 QA（stage-gate QA）。落到实践上，它是一套在 agent 配置每次变化时都会运行的测试套件。换入新模型，或者重写了 prompt，eval 套件会告诉你：agent 干活的质量是否还跟以前一样。

这套 evals 应当被当作活套件来经营。模型在进步，曾经能区分好坏的评测用例会渐渐失效，就得靠持续监控不断补进新用例。

视使用场景而定，有些团队更愿意按固定节奏离线跑这些 evals，而不是每次变更都触发。下面的步骤针对的是持续评估（continuous evaluations）。

开始之前
前置条件：CLAUDE.md 与反馈回路（阶段 4：测试）。
基础设施：能非交互式运行 Claude Code 的 CI，以及预算足以跑 eval 的 API key。

如何执行
平台工程师从近期工作中收集 20 到 50 个真实任务，每个任务都带着它的预期结果或可接受结果。
把每个任务写成一条 eval，也就是一个 prompt 加上判定"可接受"的检查项（测试通过、lint 干净、行为不变、符合策略）。
套件在 CI 中非交互式运行：既按计划任务定时跑，也在 CLAUDE.md、skills 或 hooks 发生任何变化时跑——这份配置在指挥 agent 的行为，理应获得代码享有的回归测试待遇。
配置变更以运行结果为准入闸门。一次导致通过率下降的 skill 改动，合入之前必须先接受评审。
每一起生产事故都要对应一条 eval，由事故归属团队编写，并作为回归测试常驻套件。

实际长什么样

.github/workflows/agent-evals.yml:

yaml
name: Agent evals
on:
  pull_request:
    paths: ['CLAUDE.md', '.claude/**']
  schedule:
    - cron: '0 2 * * *'
jobs:
  evals:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install -g @anthropic-ai/claude-code
      - name: Run eval suite
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          for eval in evals/*.json; do
            claude -p "$(jq -r '.prompt' $eval)" \
              --allowedTools "Read,Edit,Bash(make test)" \
              --output-format json > result.json
            ./evals/check.sh "$eval" result.json
          done

治理考量

Evals 给 QA 一扇跟得上 agent 产出的闸门：通过率阈值作为合并检查（merge check）强制执行，每次运行都会留日志，便于跨时间比较结果；配置变更由归属团队批准放行。

如何衡量
先行指标：eval 通过率随时间的变化——套件每次运行都会报告；以及一起生产事故要多久才能沉淀为一条常驻 eval。
滞后指标：CI 拦下的回归，对照事故跟踪系统中统计到的生产环境回归。
这有帮助吗？
上一课
给 Claude 一个反馈回路
下一课
AI 参与 PR 审查回路
第 9 课，共 14 课 · AI 原生 SDLC 实践手册
在 CI 里持续跑 evals
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


---
