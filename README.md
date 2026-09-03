# The AI-Native SDLC Playbook

Anthropic Claude Academy 官方课程《The AI-Native SDLC Playbook》的整理版电子书（EPUB），供离线阅读。

## 这是什么

这是一份关于**如何用 AI 改造软件开发生命周期（SDLC）**的实战指南，来自 Anthropic 官方课程（Claude Academy）。针对"AI 已经在写代码，但代码周围的流程（计划、评审、测试、部署）还停留在人速"的组织，给出 6 个阶段的完整做法：

1. **Plan** — 用 `intent.md` 捕捉想法
2. **Design** — 需求与设计
3. **Build** — Claude Code 计划模式、CLAUDE.md、Skills、并行会话与子代理
4. **Test** — 给 Claude 反馈回路、CI 中的持续评测
5. **Deploy** — AI 参与 PR 评审、Hooks 作为审批闸门、CI/CD 集成
6. **Maintain** — 闭环指标、控制带回流

## 下载

- **EPUB 版**：[ai-native-sdlc-playbook.epub](ai-native-sdlc-playbook.epub)（可在 Apple Books / Kindle / 任意阅读器打开）
- **Markdown 原稿**：[book.md](book.md)（便于阅读/编辑/再次转换）

## 内容说明

- 这是对 Anthropic 官方课程页面的**忠实内容整理**，未做改写
- 含课程全部 14 课（Introduction + 6 个阶段 + Closing）
- 原文语言：英文（本册为英文原版整理）

## 原课程

- 网址：https://academy.claude.com/courses/ai-native-sdlc-playbook
- 版权归 Anthropic 所有。本仓库仅为个人学习整理用途。

## 转换

```bash
# 从 markdown 重新生成 epub
pandoc full.md -f markdown-tex_math_dollars-tex_math_single_backslash -t epub \
  -o ai-native-sdlc-playbook.epub \
  --metadata title="The AI-Native SDLC Playbook" \
  --metadata author="Anthropic Claude Academy" \
  --toc --toc-depth=2
```
