# Prompt Localization Design (方案 B)

## Background

将 superpowers 库改造为公司内部库，团队成员普遍英文阅读能力一般。需要将用户可见的描述翻译为中文，同时保留 LLM 指令为英文以确保最佳执行效果。

已完全独立于上游，不再同步更新。

## Decision

**方案 B：只翻译用户可见的部分，LLM 指令保留英文。**

核心逻辑：提示词是写给 AI 的，不是写给人的。同事需要的是"知道有什么技能、怎么用"。

## Scope

### 翻译（用户可见）

| 文件 | 字段 | 数量 |
|------|------|------|
| `skills/*/SKILL.md` | frontmatter `description` | 15 |
| `commands/*.md` | frontmatter `description` | 3 |
| `agents/code-reviewer.md` | frontmatter `description` | 1 |
| `.claude-plugin/plugin.json` | `description`, `keywords` | 1 |

### 不翻译（LLM 指令）

- SKILL.md 主体内容（`---` 以下）
- `*-prompt.md` 子代理提示词模板
- agents/code-reviewer.md 系统提示词主体
- 代码示例、命令名、标识符

## Principles

1. description 用中文重写而非逐字翻译，简洁易懂
2. 技术术语保留英文（TDD、PR、worktree、subagent 等）
3. 命令名/技能名保留英文（用户用英文名调用）
