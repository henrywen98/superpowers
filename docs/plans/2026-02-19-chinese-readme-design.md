# 设计文档：中文 README 替换

## 目标

将 Superpowers 的 README.md 替换为中文完整教程，用于公司内部分发。

## 决策记录

| 问题 | 决策 |
|------|------|
| 目标读者 | 管理者/team lead + 开发者，前半部分讲价值，后半部分给操作指南 |
| 内容深度 | 完整教程，每个 skill 详细说明 |
| 安装方式 | 仅保留压缩包安装，Git 仓库地址待确认后补充 |
| OpenCode 安装 | 写入 README，与 Claude Code 并列 |
| 设计理念/贡献指南 | 保留设计理念（翻译），去掉贡献指南 |
| 对话示例 | 核心 skill 配示例（brainstorming、TDD、debugging、subagent-driven-development） |
| 方案选择 | 方案 A：直接替换 README.md |

## 文档结构

1. 价值介绍（这是什么 / 解决什么问题）
2. 核心工作流（7 步概览）
3. 安装指南（Claude Code + OpenCode 含 Windows）
4. 技能详解（15 个 skill 分 5 类，4 个核心 skill 配对话示例）
5. 设计理念
6. 更新方式
7. 许可证

## 特殊标注

- brainstorming 部分加入提示：可粘贴产品经理的需求文档（含图片）
- validating-plans 标注为内部自研扩展功能
- Windows 安装步骤沿用 docs/README.opencode.md 原内容
