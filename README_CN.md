---
项目来源: https://github.com/vercel-labs/add-skill
作者: Vercel Labs
许可证: MIT
翻译说明: 本文档由 Gemini CLI 翻译，旨在为中文用户提供详细的使用参考。
---

# add-skill

从任何 git 仓库为您的编程助手（Agent）安装技能。

支持 [OpenCode](https://opencode.ai), [Claude Code](https://claude.ai/code), [Codex](https://developers.openai.com/codex), [Cursor](https://cursor.com), 和 [Antigravity](https://antigravity.google)。

## 快速开始

```bash
npx add-skill vercel-labs/agent-skills
```

## 什么是 Agent Skills？

Agent skills（助手技能）是可重用的指令集，可以扩展编程助手的能力。它们定义在带有 YAML 前置数据（包含 `name` 和 `description`）的 `SKILL.md` 文件中。

技能允许助手执行专门的任务，例如：
- 从 git 历史记录生成发布说明
- 按照团队规范创建 PR
- 与外部工具（Linear, Notion 等）集成

## 使用方法

### 来源格式

`<source>` 参数接受多种格式：

```bash
# GitHub 简写
npx add-skill vercel-labs/agent-skills

# 完整的 GitHub URL
npx add-skill https://github.com/vercel-labs/agent-skills

# 仓库中特定技能的直接路径
npx add-skill https://github.com/vercel-labs/agent-skills/tree/main/skills/frontend-design

# GitLab URL
npx add-skill https://gitlab.com/org/repo

# 任何 git URL
npx add-skill git@github.com:vercel-labs/agent-skills.git
```

### 选项

| 选项 | 描述 |
|--------|-------------|
| `-g, --global` | 安装到用户目录而非项目目录 |
| `-a, --agent <agents...>` | 指定目标助手：`opencode`, `claude-code`, `codex`, `cursor`, `antigravity` |
| `-s, --skill <skills...>` | 通过名称安装特定技能 |
| `-l, --list` | 仅列出可用技能，不进行安装 |
| `-y, --yes` | 跳过所有确认提示 |
| `-V, --version` | 显示版本号 |
| `-h, --help` | 显示帮助信息 |

### 示例

```bash
# 列出仓库中的技能
npx add-skill vercel-labs/agent-skills --list

# 安装多个特定技能
npx add-skill vercel-labs/agent-skills --skill frontend-design --skill skill-creator

# 安装到特定助手
npx add-skill vercel-labs/agent-skills -a claude-code -a opencode

# 非交互式安装（CI/CD 友好）
npx add-skill vercel-labs/agent-skills --skill frontend-design -g -a claude-code -y

# 安装仓库中的所有技能
npx add-skill vercel-labs/agent-skills -y -g
```

## 安装路径

技能根据助手和范围安装到不同的位置：

### 项目级别（默认）

安装在当前工作目录中。请将其提交到代码库以与团队共享。

| 助手 | 路径 |
|-------|------|
| OpenCode | `.opencode/skill/<name>/` |
| Claude Code | `.claude/skills/<name>/` |
| Codex | `.codex/skills/<name>/` |
| Cursor | `.cursor/skills/<name>/` |
| Antigravity | `.agent/skills/<name>/` |

### 全局级别 (`--global`)

安装在您的主目录中。可在所有项目中通用。

| 助手 | 路径 |
|-------|------|
| OpenCode | `~/.config/opencode/skill/<name>/` |
| Claude Code | `~/.claude/skills/<name>/` |
| Codex | `~/.codex/skills/<name>/` |
| Cursor | `~/.cursor/skills/<name>/` |
| Antigravity | `~/.gemini/antigravity/skills/<name>/` |

## 助手检测

CLI 会通过检查配置目录自动检测您安装了哪些编程助手。如果未检测到任何助手，系统会提示您选择要安装到的助手。

## 创建技能

技能是包含带有 YAML 前置数据的 `SKILL.md` 文件的目录：

```markdown
---
name: my-skill
description: 该技能的作用以及何时使用它
---

# 我的技能 (My Skill)

当此技能被激活时，助手需要遵循的指令。

## 何时使用

描述应使用此技能的场景。

## 步骤

1. 首先，执行此操作
2. 然后，执行彼操作
```

### 必需字段

- `name`: 唯一标识符（小写，允许连字符）
- `description`: 技能作用的简要说明

### 技能发现

CLI 会在仓库中的以下位置搜索技能：

- 根目录（如果包含 `SKILL.md`）
- `skills/`
- `skills/.curated/`
- `skills/.experimental/`
- `skills/.system/`
- `.codex/skills/`
- `.claude/skills/`
- `.opencode/skill/`
- `.cursor/skills/`
- `.agent/skills/`

如果在标准位置未找到技能，将进行递归搜索。

## 兼容性

技能通常在不同助手之间通用，因为它们遵循共享的 [Agent Skills 规范](https://agentskills.io)。但是，某些功能可能是助手特定的：

| 功能 | OpenCode | Claude Code | Codex | Cursor | Antigravity |
|---------|----------|-------------|-------|--------|-------------|
| 基础技能 | 是 | 是 | 是 | 是 | 是 |
| `allowed-tools` | 是 | 是 | 是 | 是 | 是 |
| `context: fork` | 否 | 是 | 否 | 否 | 否 |
| 钩子 (Hooks) | 否 | 是 | 否 | 否 | 否 |

## 故障排除

### "未找到技能" (No skills found)

确保仓库中包含有效的 `SKILL.md` 文件，且其前置数据中包含 `name` 和 `description`。

### 技能未在助手中加载

- 验证技能是否安装到了正确的路径
- 查看助手的文档以了解技能加载要求
- 确保 `SKILL.md` 的前置数据是有效的 YAML

### 权限错误

确保您对目标目录具有写入权限。

## 相关链接

- [Vercel Agent Skills 仓库](https://github.com/vercel-labs/agent-skills)
- [Agent Skills 规范](https://agentskills.io)
- [OpenCode 技能文档](https://opencode.ai/docs/skills)
- [Claude Code 技能文档](https://code.claude.com/docs/en/skills)
- [Codex 技能文档](https://developers.openai.com/codex/skills)
- [Cursor 技能文档](https://cursor.com/docs/context/skills)
- [Antigravity 技能文档](https://antigravity.google/docs/skills)

## 许可证

MIT
