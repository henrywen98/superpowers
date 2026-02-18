# finishing-a-development-branch Skill 精简设计

## 背景

当前 skill 设计面向 GitHub 工作流，提供 4 个选项（merge/PR/保留/丢弃）并自动执行 git 操作。但：

1. 公司内部不使用 GitHub，`gh` CLI 相关功能不可用
2. commit/merge/push 等操作应由人工执行，AI 不应代为操作

## 设计目标

将 skill 从「操作执行器」转变为「完成前检查报告」。AI 只做只读操作（跑测试、收集状态），不执行任何写入性 git 操作。

## 新流程

```
跑测试 → 通过？→ 生成状态汇总报告 → 提醒 worktree 清理 → 结束
```

### Step 1: 跑测试

- AI 执行项目测试命令
- 失败 → 报告失败详情，停止
- 通过 → 继续

### Step 2: 生成状态报告

AI 自动收集并输出：

- 分支名
- Base branch
- Commit 列表
- 变更文件统计（diff stat）
- 测试结果

### Step 3: Worktree 提醒

- 如果当前在 worktree 中 → 提醒用户手动清理
- 否则跳过

### Step 4: 交棒

输出："状态汇总完毕，后续的 commit/merge/push 请手动操作。"

## 移除的内容

- 4 选项菜单
- 所有 git merge/push/checkout/branch -d 执行逻辑
- `gh pr create` 相关逻辑
- 丢弃工作的确认流程

## 对比

| 维度 | 原版 | 新版 |
|------|------|------|
| 角色 | 操作执行器 | 完成前检查报告 |
| 执行 git 写操作 | 是 | 否 |
| 选项菜单 | 4 个 | 无 |
| 外部依赖 | `gh` CLI | 无 |
| AI 执行的操作 | 测试 + git 操作 | 仅测试 + 信息收集 |
