# 阶段 3 · CLAUDE.md

AI 原生 SDLC 实践手册
CLAUDE.md
第 5 课
3 分钟

CLAUDE.md 给 Claude 提供的，是新人入职第一天需要的那类上下文：约定、命令、架构，以及团队最常踩的坑。过去存在人脑里、散落在 wiki 上的知识，变成了一份文件——agent 每次会话开始都会读它，整个团队共同维护，每犯一次错就迭代一次。

开始之前
前置条件：无。
基础设施：一个仓库、装好的 Claude Code，以及一名熟悉代码库的工程师。
怎么执行
在仓库里运行 /init。Claude 会根据它看到的内容生成一份 CLAUDE.md 初稿。
把生成的文件删减到"新人第一天需要"的程度。保留 build、test、lint 命令、真正重要的约定，以及 Claude 反复出错的地方。
把 CLAUDE.md 提交进仓库根目录的 Git，让整个团队共享同一版本，变更像代码一样被评审。
这里有一条好用的小规则：当 Claude 犯同一个错误两次，就把纠正写进 CLAUDE.md。
让它控制在一页以内——因为 Claude 在会话开始时会把整份文件读完，任何过时的内容都在白白占用上下文。

它长什么样

CLAUDE.md：

markdown

# 支付服务
## Commands
- Build: make build
- Test: make test (unit), make itest (integration, needs docker)
- Lint: make lint (runs in CI; fix before pushing)
## Conventions
- Java 21, Spring Boot 3. No new Lombok.
- Money is always BigDecimal, never double.
- Every endpoint needs an integration test in src/itest.
## Architecture
- api/ holds REST controllers, core/ holds domain logic,
  adapters/ talks to external systems.
- Kafka events are defined in schemas/; never edit generated classes.
## Things Claude gets wrong
- Do not bump dependency versions; the platform team owns them.
- The legacy v1/ package is frozen; changes go in v2/.
治理方面的考量

CLAUDE.md 受版本控制，所以 agent 依据的指令是可评审、可审计的。团队约定通过这份文件落地，对它的修改会记录在 Git 历史里，代码所有者（code owner）在 PR 评审中批准这些变更。

怎么衡量
领先指标：Claude 重复犯 CLAUDE.md 本应拦下的错误的频率。对 CLAUDE.md 的修正或改动应记录在 Git 历史中。
滞后指标：从 PR 历史看，团队新成员从加入到达成第一个合入的 PR 所需的时间。
这有帮助吗？
上一课
把 Claude Code plan mode 作为默认起点
下一课
把 Skills 当作机构知识（institutional knowledge）
第 5 课，共 14 课 · AI 原生 SDLC 实践手册
CLAUDE.md
导言
导言
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
开始之前
怎么执行
它长什么样
治理方面的考量
怎么衡量


---
