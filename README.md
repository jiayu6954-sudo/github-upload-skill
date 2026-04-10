# github-upload-skill

> 中文 | [English](README.en.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-blue)](https://claude.ai/code)
[![GitHub Automation](https://img.shields.io/badge/GitHub-Automation-green)](https://github.com/jiayu6954-sudo/github-upload-skill)

一个由 AI 驱动的 Claude Code Skill，让任何本地项目都能**一键完成打包检查 → 新建仓库 → 推送**的完整流程。

---

## 它和 `gh`、`auto-publish-github` 有什么区别？

> **一句话**：它不是一个独立的命令行工具，而是一个由 AI 驱动的、包含标准化流程和真实踩坑经验的**交互式指南**。

| 对比维度 | `gh` CLI | 本 Skill |
|---------|---------|---------|
| 类型 | 独立命令行工具 | Claude Code AI 交互式指南 |
| 安装 | 需要单独安装二进制 | 复制一个 `.md` 文件即可 |
| 流程引导 | 只执行命令，不引导 | 逐阶段引导，关键节点主动预检 |
| 敏感文件扫描 | 不包含 | ✅ 内置，发现问题主动拦截 |
| LICENSE 预检 | 不包含 | ✅ 自动检测 `[Author]` 占位符 |
| 推送失败处理 | 报错后需自行排查 | ✅ 内置自愈指南 + 自动重试命令 |
| 踩坑经验 | 无 | ✅ 来自真实项目的 10+ 个已知坑 |

---

## 快速安装（30 秒）

```bash
# 1. 克隆或直接下载 github-upload.md
# 2. 复制到 Claude Code skills 目录

# Windows
copy github-upload.md %USERPROFILE%\.claude\skills\github-upload.md

# macOS / Linux
cp github-upload.md ~/.claude/skills/github-upload.md
```

安装后在 Claude Code 中输入 `/github-upload` 即可触发。

---

## 核心功能

### 智能预检（阶段一新增）

不再依赖人工肉眼检查，AI 会自动执行并解读结果：

- **LICENSE 占位符检测**：自动检测 `[Author]` 是否已替换，未替换则主动拦截，修复后方可继续
- **敏感文件扫描**：扫描源文件中的密码/密钥关键字，发现即停止
- **标准文件完整性**：README / LICENSE / .gitignore 缺失时提示并提供模板

### 失败自愈（阶段五新增）

推送失败不再手足无措：

| 失败类型 | 自愈方案 |
|---------|---------|
| 网络偶发超时 | 30 秒后自动重试（内置重试脚本）|
| 代理/防火墙拦截 | 切换为 SSH 方式的指引 |
| Token 过期 | 直接跳转到重新生成页面 |
| 远程冲突 | `git pull --rebase` 后再 push |

---

## 完整流程（6 个阶段）

```
阶段一：智能打包前检查  → LICENSE预检 + 敏感文件扫描 + 标准文件验证
阶段二：凭证绑定        → Token 永久存储，只需操作一次
阶段三：新建仓库        → GitHub API 创建，绝不影响已有仓库
阶段四：Git 初始化      → git init + remote add + 地址验证
阶段五：提交推送        → git add + commit + push + 失败自愈
阶段六：结果验证        → 逐项核查文件、渲染、敏感内容
```

---

## 适用范围

- **任何语言**：Python / Java / Go / TypeScript / Rust / C++ …
- **任何规模**：个人项目、团队项目、开源库
- **任何系统**：Windows / macOS / Linux
- **任何 AI 工具**：`github-upload.md` 本身可复制粘贴给任何 LLM 使用

---

## 已知踩坑（来自真实使用）

| 坑 | 现象 | 解决 |
|----|------|------|
| curl description 含中文 | `Problems parsing JSON` | 改纯英文，JSON 用单引号包裹 |
| 误填已有仓库地址 | 覆盖他人代码 | `git remote -v` 确认地址后再 push |
| Token 缺少 `repo` 权限 | `403 Forbidden` | 重新生成 Token，勾选 `repo` |
| LICENSE 未替换 `[Author]` | 无法律效力 | 阶段一智能预检会主动拦截 |
| 网络偶发失败 | push 中断 | 内置重试脚本，自动等待重试 |

---

## 文件说明

| 文件 | 说明 |
|------|------|
| `github-upload.md` | ⭐ **核心文件**：完整 Skill，复制到 skills 目录即可使用 |
| `README.md` | 本文件（中文说明）|
| `README.en.md` | 英文说明 |
| `LICENSE` | MIT License |
| `CONTRIBUTING.md` | 贡献指南（欢迎补充踩坑）|

---

## 所属项目矩阵

本 Skill 是 **[AI-Project-OS](https://github.com/jiayu6954-sudo/AI-Project-OS)** 的子项目。  
AI-Project-OS 是一套完整的工业级 AI 协作工程方法论，包含 6 阶段工作流、代码审查清单、约束提示词库和案例知识库。

---

## License

[MIT License](LICENSE) © 2026 jiayu6954-sudo

---

*版本：v1.2 · 更新：2026-04-10*  
*关键词：claude-code-skill · github-automation · ai-workflow · git-upload · one-click-deploy*
