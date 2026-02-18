# Prompt Localization Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 将所有用户可见的 description 字段翻译为中文，LLM 指令保留英文。

**Architecture:** 纯文本编辑，修改 YAML frontmatter 中的 description 字段和 plugin.json 的描述。

**Tech Stack:** Markdown YAML frontmatter, JSON

---

### Task 1: Skills description 翻译（第一批 8 个）

**Files:**
- Modify: `skills/brainstorming/SKILL.md:3`
- Modify: `skills/dispatching-parallel-agents/SKILL.md:3`
- Modify: `skills/executing-plans/SKILL.md:3`
- Modify: `skills/finishing-a-development-branch/SKILL.md:3`
- Modify: `skills/receiving-code-review/SKILL.md:3`
- Modify: `skills/requesting-code-review/SKILL.md:3`
- Modify: `skills/subagent-driven-development/SKILL.md:3`
- Modify: `skills/systematic-debugging/SKILL.md:3`

**Step 1: 修改 description 字段**

逐个修改以下 SKILL.md 第 3 行的 description：

| 文件 | 原文 | 中文 |
|------|------|------|
| brainstorming | You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation. | 任何创造性工作前必须使用 — 创建功能、构建组件、添加特性或修改行为前，先探索意图、需求和设计 |
| dispatching-parallel-agents | Use when facing 2+ independent tasks that can be worked on without shared state or sequential dependencies | 面对 2 个以上互不依赖的任务时使用，并行分发给子代理执行 |
| executing-plans | Use when you have a written implementation plan to execute in a separate session with review checkpoints | 有现成的实施计划时使用，在独立会话中按步骤执行并设置检查点 |
| finishing-a-development-branch | Use when implementation is complete, all tests pass, and you need to decide how to integrate the work - guides completion of development work by presenting structured options for merge, PR, or cleanup | 实现完成且测试通过后使用 — 引导选择 merge、PR 或清理等集成方式 |
| receiving-code-review | Use when receiving code review feedback, before implementing suggestions, especially if feedback seems unclear or technically questionable - requires technical rigor and verification, not performative agreement or blind implementation | 收到 code review 反馈后使用 — 先技术验证再实施，不盲目接受也不敷衍同意 |
| requesting-code-review | Use when completing tasks, implementing major features, or before merging to verify work meets requirements | 完成任务、实现重要功能或 merge 前使用，验证工作是否符合需求 |
| subagent-driven-development | Use when executing implementation plans with independent tasks in the current session | 在当前会话中执行有多个独立任务的实施计划时使用 |
| systematic-debugging | Use when encountering any bug, test failure, or unexpected behavior, before proposing fixes | 遇到 bug、测试失败或异常行为时使用，在提出修复前先系统排查 |

**Step 2: Commit**

```bash
git add skills/brainstorming/SKILL.md skills/dispatching-parallel-agents/SKILL.md skills/executing-plans/SKILL.md skills/finishing-a-development-branch/SKILL.md skills/receiving-code-review/SKILL.md skills/requesting-code-review/SKILL.md skills/subagent-driven-development/SKILL.md skills/systematic-debugging/SKILL.md
git commit -m "feat: 翻译第一批 skills description 为中文"
```

---

### Task 2: Skills description 翻译（第二批 7 个）

**Files:**
- Modify: `skills/test-driven-development/SKILL.md:3`
- Modify: `skills/using-git-worktrees/SKILL.md:3`
- Modify: `skills/using-superpowers/SKILL.md:3`
- Modify: `skills/verification-before-completion/SKILL.md:3`
- Modify: `skills/writing-plans/SKILL.md:3`
- Modify: `skills/writing-skills/SKILL.md:3`
- Modify: `skills/validating-plans/SKILL.md:3`

**Step 1: 修改 description 字段**

| 文件 | 原文 | 中文 |
|------|------|------|
| test-driven-development | Use when implementing any feature or bugfix, before writing implementation code | 实现任何功能或修复 bug 前使用，先写测试再写代码（TDD） |
| using-git-worktrees | Use when starting feature work that needs isolation from current workspace or before executing implementation plans - creates isolated git worktrees with smart directory selection and safety verification | 需要隔离开发环境时使用 — 创建 git worktree，智能选择目录并做安全检查 |
| using-superpowers | Use when starting any conversation - establishes how to find and use skills, requiring Skill tool invocation before ANY response including clarifying questions | 每次对话开始时使用 — 建立技能查找和使用机制，要求在任何响应前先调用 Skill 工具 |
| verification-before-completion | Use when about to claim work is complete, fixed, or passing, before committing or creating PRs - requires running verification commands and confirming output before making any success claims; evidence before assertions always | 声称工作完成前使用 — 必须先运行验证命令确认输出，用证据说话 |
| writing-plans | Use when you have a spec or requirements for a multi-step task, before touching code | 有需求或规格说明的多步骤任务时使用，动手写代码之前先写实施计划 |
| writing-skills | Use when creating new skills, editing existing skills, or verifying skills work before deployment | 创建、编辑技能或部署前验证技能时使用 |
| validating-plans | Use after writing-plans completes to validate plan-design consistency before execution - also use when a plan has been modified and needs re-validation | writing-plans 完成后使用，在执行前验证计划与设计的一致性；计划修改后也需要重新验证 |

**Step 2: Commit**

```bash
git add skills/test-driven-development/SKILL.md skills/using-git-worktrees/SKILL.md skills/using-superpowers/SKILL.md skills/verification-before-completion/SKILL.md skills/writing-plans/SKILL.md skills/writing-skills/SKILL.md skills/validating-plans/SKILL.md
git commit -m "feat: 翻译第二批 skills description 为中文"
```

---

### Task 3: Commands 和 Agent description 翻译

**Files:**
- Modify: `commands/brainstorm.md:2`
- Modify: `commands/execute-plan.md:2`
- Modify: `commands/write-plan.md:2`
- Modify: `agents/code-reviewer.md:3-4`

**Step 1: 修改 commands description**

| 文件 | 原文 | 中文 |
|------|------|------|
| brainstorm.md | You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores requirements and design before implementation. | 任何创造性工作前必须使用 — 先探索需求和设计再动手实现 |
| execute-plan.md | Execute plan in batches with review checkpoints | 分批执行实施计划，每批之间设置检查点 |
| write-plan.md | Create detailed implementation plan with bite-sized tasks | 创建详细的实施计划，拆分为小粒度任务 |

**Step 2: 修改 agent description**

agents/code-reviewer.md 的 description 翻译为：

```
在完成重要项目步骤后使用此 agent，对照原始计划和编码规范进行代码审查。示例：<example>Context: 用户正在创建一个 code-review agent，应在每个逻辑代码块完成后调用。user: "I've finished implementing the user authentication system as outlined in step 3 of our plan" assistant: "Great work! Now let me use the code-reviewer agent to review the implementation against our plan and coding standards" <commentary>主要项目步骤已完成，使用 code-reviewer agent 对照计划验证实现并发现问题。</commentary></example> <example>Context: 用户完成了一个重要功能的实现。user: "The API endpoints for the task management system are now complete - that covers step 2 from our architecture document" assistant: "Excellent! Let me have the code-reviewer agent examine this implementation to ensure it aligns with our plan and follows best practices" <commentary>计划文档中的某个编号步骤已完成，code-reviewer agent 应审查该工作。</commentary></example>
```

注意：agent description 中的 example 标签内保留英文对话（因为这些是 LLM 触发条件的示例），只翻译 Context 说明和 commentary。

**Step 3: Commit**

```bash
git add commands/brainstorm.md commands/execute-plan.md commands/write-plan.md agents/code-reviewer.md
git commit -m "feat: 翻译 commands 和 agent description 为中文"
```

---

### Task 4: plugin.json 翻译

**Files:**
- Modify: `.claude-plugin/plugin.json`

**Step 1: 更新 plugin.json**

```json
{
  "name": "superpowers",
  "description": "Claude Code 核心技能库：TDD、调试、协作模式和实践经验",
  "version": "4.3.0",
  "license": "MIT",
  "keywords": ["skills", "tdd", "debugging", "collaboration", "best-practices", "workflows"]
}
```

注意：keywords 保留英文（这些是机器使用的标签）。

**Step 2: Commit**

```bash
git add .claude-plugin/plugin.json
git commit -m "feat: 翻译 plugin.json description 为中文"
```
