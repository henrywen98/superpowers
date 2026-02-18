# Superpowers — AI 编码助手的完整开发工作流

Superpowers 是一套为 AI 编码助手（Claude Code / OpenCode）设计的开发工作流系统。它通过一组可组合的「技能（Skills）」，让你的 AI 助手不再是一个只会写代码的工具，而是一个遵循工程纪律的开发搭档。

## 它解决什么问题？

直接让 AI 写代码，常见的问题是：

- 需求没理清就动手，写出来的东西不是你想要的
- 不写测试，或者先写代码再补测试（形同虚设）
- 遇到 bug 靠猜，改了三四次还没修对
- 声称「已完成」但实际没验证
- 大任务一口气写完，出了问题很难定位

**Superpowers 通过自动触发的技能来解决这些问题。** 你不需要记住任何命令或流程——当 AI 检测到你要做某件事时，它会自动启用对应的技能，按照经过验证的工程实践来工作。

## 核心工作流

一个典型的功能开发流程如下：

1. **需求探索（brainstorming）** — 你说「帮我加个 XX 功能」，AI 不会直接写代码，而是先问你一系列问题，理解你真正想要什么，提出 2-3 种方案让你选，最终输出一份你确认过的设计文档。

   > 💡 你可以直接把产品经理的需求文档内容复制粘贴进来（包括图片），AI 会基于这些材料和你展开讨论。

2. **创建工作区（using-git-worktrees）** — 设计确认后，自动在独立的 git worktree 中创建新分支，安装依赖，验证测试基线通过。你的主分支不受影响。

3. **编写计划（writing-plans）** — 把设计拆成一个个 2-5 分钟就能完成的小任务，每个任务都有明确的文件路径、代码示例和验证步骤。计划写完后自动运行三项一致性检查（validating-plans），确保计划和设计吻合。

   > 📝 validating-plans 是内部自研的扩展功能，非 Superpowers 原版自带。

4. **执行开发（subagent-driven-development）** — 为每个任务派出独立的子代理执行，每完成一个任务自动进行两轮审查（规格合规 + 代码质量）。你可以让 AI 连续自主工作数小时而不偏离计划。

5. **测试驱动（test-driven-development）** — 贯穿整个开发过程。严格的 RED-GREEN-REFACTOR：先写失败测试 → 看它失败 → 写最少代码让它通过 → 重构。

6. **代码审查（requesting-code-review）** — 每个任务完成后自动触发，按严重程度分级报告问题。关键问题必须立即修复才能继续。

7. **完成分支（finishing-a-development-branch）** — 所有任务完成后，验证测试通过，提供 4 个选项：合并到主分支 / 推送并创建 PR / 保留分支 / 丢弃。

**所有技能自动触发，无需手动调用。** 你只需要像平常一样和 AI 对话，它会在合适的时机启用对应的工作流。

## 安装

### Claude Code

1. 下载插件 zip 包并解压：

```bash
unzip superpowers.zip -d ~/superpowers
```

2. 安装插件：

```bash
/plugin install ~/superpowers
```

3. 启动新会话，试试触发一个技能（比如「帮我规划这个功能」或「帮我调试这个问题」），AI 应该会自动调用对应的技能。

### OpenCode

#### macOS / Linux

```bash
# 1. 解压
unzip superpowers.zip -d ~/.config/opencode/superpowers

# 2. 创建目录
mkdir -p ~/.config/opencode/plugins ~/.config/opencode/skills

# 3. 清理旧链接（重装时安全操作）
rm -f ~/.config/opencode/plugins/superpowers.js
rm -rf ~/.config/opencode/skills/superpowers

# 4. 创建符号链接
ln -s ~/.config/opencode/superpowers/.opencode/plugins/superpowers.js ~/.config/opencode/plugins/superpowers.js
ln -s ~/.config/opencode/superpowers/skills ~/.config/opencode/skills/superpowers

# 5. 重启 OpenCode
```

验证安装：

```bash
ls -l ~/.config/opencode/plugins/superpowers.js
ls -l ~/.config/opencode/skills/superpowers
```

两个路径都应显示为符号链接。

#### Windows

**前置要求：**
- 安装了 Git
- 开启了**开发者模式**（设置 → 系统 → 开发者选项），或以**管理员身份**运行终端

**Command Prompt（以管理员运行）：**

```cmd
:: 1. 将 superpowers.zip 解压到 %USERPROFILE%\.config\opencode\superpowers

:: 2. 创建目录
mkdir "%USERPROFILE%\.config\opencode\plugins" 2>nul
mkdir "%USERPROFILE%\.config\opencode\skills" 2>nul

:: 3. 清理旧链接
del "%USERPROFILE%\.config\opencode\plugins\superpowers.js" 2>nul
rmdir "%USERPROFILE%\.config\opencode\skills\superpowers" 2>nul

:: 4. 创建插件符号链接
mklink "%USERPROFILE%\.config\opencode\plugins\superpowers.js" "%USERPROFILE%\.config\opencode\superpowers\.opencode\plugins\superpowers.js"

:: 5. 创建技能目录链接
mklink /J "%USERPROFILE%\.config\opencode\skills\superpowers" "%USERPROFILE%\.config\opencode\superpowers\skills"

:: 6. 重启 OpenCode
```

**PowerShell（以管理员运行）：**

```powershell
# 1. 解压
Expand-Archive -Path superpowers.zip -DestinationPath "$env:USERPROFILE\.config\opencode\superpowers"

# 2. 创建目录
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode\plugins"
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode\skills"

# 3. 清理旧链接
Remove-Item "$env:USERPROFILE\.config\opencode\plugins\superpowers.js" -Force -ErrorAction SilentlyContinue
Remove-Item "$env:USERPROFILE\.config\opencode\skills\superpowers" -Force -ErrorAction SilentlyContinue

# 4. 创建插件符号链接
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.config\opencode\plugins\superpowers.js" -Target "$env:USERPROFILE\.config\opencode\superpowers\.opencode\plugins\superpowers.js"

# 5. 创建技能目录链接
New-Item -ItemType Junction -Path "$env:USERPROFILE\.config\opencode\skills\superpowers" -Target "$env:USERPROFILE\.config\opencode\superpowers\skills"

# 6. 重启 OpenCode
```

**Git Bash：**

```bash
# 1. 解压
unzip superpowers.zip -d ~/.config/opencode/superpowers

# 2. 创建目录
mkdir -p ~/.config/opencode/plugins ~/.config/opencode/skills

# 3. 清理旧链接
rm -f ~/.config/opencode/plugins/superpowers.js 2>/dev/null
rm -rf ~/.config/opencode/skills/superpowers 2>/dev/null

# 4. 创建插件符号链接（Git Bash 的 ln 会复制文件，必须用 mklink）
cmd //c "mklink \"$(cygpath -w ~/.config/opencode/plugins/superpowers.js)\" \"$(cygpath -w ~/.config/opencode/superpowers/.opencode/plugins/superpowers.js)\""

# 5. 创建技能目录链接
cmd //c "mklink /J \"$(cygpath -w ~/.config/opencode/skills/superpowers)\" \"$(cygpath -w ~/.config/opencode/superpowers/skills)\""

# 6. 重启 OpenCode
```

> 如果在 WSL 中使用 OpenCode，请按 macOS / Linux 的步骤操作。

#### Windows 常见问题

| 问题 | 解决方法 |
|------|----------|
| "You do not have sufficient privilege" | 开启开发者模式，或以管理员身份运行终端 |
| "Cannot create a file when that file already exists" | 先运行步骤 3 清理旧链接，再重试 |
| 符号链接在 git clone 后失效 | 运行 `git config --global core.symlinks true` 后重新 clone |

#### 验证 OpenCode 安装

```bash
# 检查插件加载
opencode run --print-logs "hello" 2>&1 | grep -i superpowers

# 检查技能是否可发现
opencode run "use skill tool to list all skills" 2>&1 | grep -i superpowers

# 检查引导注入
opencode run "what superpowers do you have?"
```

## 技能详解

Superpowers 包含 15 个技能，分为 5 类。所有技能在合适的场景下自动触发，无需手动调用。

---

### 协作与设计类

#### brainstorming — 需求探索与设计

**何时触发：** 任何涉及创建功能、构建组件、添加新行为的对话开始时自动触发。

**做什么：**
- 先检查项目上下文（文件、文档、最近的提交记录）
- 每次只问你一个问题（优先给选择题），逐步理清需求
- 提出 2-3 种方案，分析各自的取舍，给出推荐
- 将设计分段展示，每段确认后再继续
- 最终将确认的设计保存到 `docs/plans/YYYY-MM-DD-<主题>-design.md`

**核心原则：** 无论项目看起来多简单，都必须先过设计环节。「简单」项目恰恰是未经检验的假设最容易浪费时间的地方。

<details>
<summary>💬 对话示例</summary>

```
👤 帮我加一个用户导出功能，把数据导出成 CSV

🤖 好的，我先了解一下需求。你希望导出哪些数据？
   A. 用户列表（姓名、邮箱、注册时间等基本信息）
   B. 用户的操作记录
   C. 两者都要
   D. 其他

👤 A

🤖 导出的触发方式你倾向哪种？
   A. 管理后台页面上加一个「导出」按钮，点击直接下载
   B. 提供 API 接口，前端自行调用
   C. 两者都要

👤 A

🤖 了解。关于数据量，目前用户表大概有多少条记录？
   这会影响我选择同步下载还是异步任务 + 文件下载的方案。

👤 大概 5000 左右，不会太大

🤖 好的，我来提 2 个方案...
   【方案 A: 同步导出】...
   【方案 B: 异步队列】...
   我推荐方案 A，原因是...
```

</details>

#### writing-plans — 编写实施计划

**何时触发：** 设计文档确认后，需要将设计拆解为可执行的任务时。

**做什么：**
- 将设计拆成一个个 2-5 分钟可完成的小任务
- 每个任务包含：要修改的文件路径、完整的代码示例、精确的验证命令
- 假设执行者没有项目上下文，所有信息必须自包含
- 计划保存到 `docs/plans/YYYY-MM-DD-<功能名>.md`
- 贯彻 TDD（测试先行）、YAGNI（不做不需要的）、DRY（不重复）原则

#### validating-plans — 计划验证

> 📝 内部自研的扩展功能，非 Superpowers 原版自带。

**何时触发：** 计划编写完成后自动运行。

**做什么：**
- 同时派出 3 个独立检查器并行验证：
  - **覆盖度检查** — 设计文档中的每个需求是否都有对应的任务
  - **范围检查** — 计划中是否有超出设计范围的任务
  - **意图检查** — 实现方式是否符合设计中的架构决策
- 如果检查不通过，自动修复并重新验证（最多 3 轮）
- 所有检查通过后才允许进入执行阶段

#### executing-plans — 分批执行计划

**何时触发：** 当你要在单独的会话中执行已有计划时。

**做什么：**
- 加载计划，先整体审查并提出疑虑
- 每批执行前 3 个任务（默认），每个任务按步骤精确执行
- 每批完成后报告进展和验证结果，等你确认后再继续下一批
- 遇到阻塞、计划缺漏、指令不明确时会主动停下来问你

#### subagent-driven-development — 子代理驱动开发

**何时触发：** 在当前会话中执行计划，且任务之间相互独立时。

**做什么：**
- 为每个任务派出一个全新的子代理，提供完整上下文
- 子代理完成后，自动进行两轮审查：
  1. **规格合规审查** — 代码是否完全匹配任务规格
  2. **代码质量审查** — 代码质量、可读性、最佳实践
- 审查发现问题则由子代理修复后重新审查
- 所有任务完成后进行最终全局代码审查
- 整个过程可以让 AI 连续自主工作数小时

<details>
<summary>💬 对话示例</summary>

```
👤 按照计划开始开发吧

🤖 好的，我来执行计划。共 6 个任务，我会为每个任务派出子代理。

   📋 任务 1/6: 创建用户模型和数据库迁移
   ├── 🚀 派出实现子代理...
   ├── ✅ 实现完成，代码已提交
   ├── 🔍 规格审查: 通过
   └── 🔍 代码质量审查: 发现 1 个问题 → 已修复 → 重新审查通过

   📋 任务 2/6: 创建导出 API 端点
   ├── 🚀 派出实现子代理...
   ├── ✅ 实现完成，代码已提交
   ├── 🔍 规格审查: 通过
   └── 🔍 代码质量审查: 通过

   ...继续执行后续任务...
```

</details>

---

### 测试类

#### test-driven-development — 测试驱动开发

**何时触发：** 实现任何功能或修复 bug 时，在写实现代码之前自动触发。

**做什么：**

严格执行 RED-GREEN-REFACTOR 循环：

1. **RED（红灯）** — 写一个最小的失败测试，只测一个行为，命名清晰
2. **验证红灯** — 运行测试，确认它以预期的方式失败
3. **GREEN（绿灯）** — 写最简单的代码让测试通过，不多不少
4. **验证绿灯** — 运行测试，确认全部通过
5. **REFACTOR（重构）** — 在测试保持绿灯的前提下清理代码

**核心原则：** 如果你没有亲眼看到测试失败，你就不知道它是否在测试正确的东西。如果先写了实现代码再补测试，必须删掉重来。

**危险信号：**
- 代码写在测试之前
- 测试写完直接就通过了（说明没测到新行为）
- 一次改多个东西
- 无法解释测试为什么会失败

<details>
<summary>💬 对话示例</summary>

```
👤 帮我写一个邮箱格式验证函数

🤖 好的，按照 TDD 流程来。先写测试：

   // test_email_validator.py
   def test_rejects_empty_string():
       assert validate_email("") == False

   运行测试...
   ❌ FAILED: NameError: name 'validate_email' is not defined

   红灯确认，测试以预期方式失败。现在写最小实现：

   def validate_email(email):
       return False

   运行测试...
   ✅ PASSED

   绿灯。继续下一个行为——验证合法邮箱：

   def test_accepts_valid_email():
       assert validate_email("user@example.com") == True

   运行测试...
   ❌ FAILED: AssertionError: assert False == True

   红灯确认。扩展实现...
```

</details>

#### verification-before-completion — 完成前验证

**何时触发：** 当 AI 即将宣称工作完成、bug 已修复、测试已通过时自动触发。

**做什么：**
- 识别需要运行的验证命令
- 运行完整命令（不是部分运行）
- 读取完整输出（不是只看最后一行）
- 确认输出与声明一致后，才做出完成声明

**核心原则：** 「证据先于断言」。声称完成而没有验证，不是高效，是不诚实。

**禁止出现的措辞：**
- "应该可以了"
- "我很确定"
- "看起来没问题"
- 任何没有运行验证就做出的成功声明

---

### 调试类

#### systematic-debugging — 系统化调试

**何时触发：** 遇到任何 bug、测试失败或意外行为时，在提出修复方案之前自动触发。

**做什么：**

分 4 个阶段系统排查：

1. **阶段一：收集证据** — 读错误信息、复现问题、检查最近改动、收集相关证据
2. **阶段二：模式分析** — 找到正常工作的案例、对比差异、查找参考实现
3. **阶段三：假设与验证** — 形成单一假设、做最小化测试验证
4. **阶段四：实施修复** — 先写失败测试、实施单一修复、完整验证

**核心原则：** 如果连续 3 次修复都没成功，停下来质疑架构层面的问题，而不是继续在症状上打补丁。

**危险信号：**
- "快速修一下"
- "试试看"
- "大概是因为..."
- 一次改多个地方

<details>
<summary>💬 对话示例</summary>

```
👤 用户登录接口报 500 了，帮我看看

🤖 开始系统化调试。

   📍 阶段一：收集证据
   ├── 读取错误日志... KeyError: 'refresh_token' at auth.py:42
   ├── 复现: curl POST /api/login → 500 ✅ 可复现
   ├── 最近改动: git log --since="2 days ago" -- auth.py
   │   → 昨天有一次 token 结构重构
   └── 证据收集完毕

   📍 阶段二：模式分析
   ├── 重构前的 token 结构: {"access_token": ..., "refresh_token": ...}
   ├── 重构后的 token 结构: {"accessToken": ..., "refreshToken": ...}
   └── auth.py:42 仍在用旧的 key 名 "refresh_token"

   📍 阶段三：假设
   └── 假设: token 结构重构后，auth.py 的 key 名没有同步更新

   📍 阶段四：验证修复
   ├── 先写失败测试...
   ├── 修改 auth.py:42 的 key 名
   ├── 测试通过 ✅
   └── 完整回归测试通过 ✅
```

</details>

---

### 工程实践类

#### using-git-worktrees — Git 工作区管理

**何时触发：** 开始需要隔离的功能开发，或执行实施计划之前。

**做什么：**
- 在独立的 git worktree 中创建新分支，共享同一个仓库但互不干扰
- 自动检测项目类型并运行安装命令（npm install / cargo build / pip install 等）
- 验证测试基线在新工作区中通过
- 确保工作区目录已加入 `.gitignore`

**好处：** 你可以在 AI 开发新功能的同时，在主工作区继续做其他事情。

#### dispatching-parallel-agents — 并行子代理调度

**何时触发：** 面对 2 个以上相互独立、没有共享状态的任务时。

**做什么：**
- 识别可以并行处理的独立领域（比如不同文件的测试失败、不相关的 bug）
- 为每个领域创建专注的子代理任务，包含明确的范围、目标和约束
- 同时派出所有子代理并行工作
- 收集结果，验证修复之间不冲突

**适用场景：** 多个不相关的测试文件失败、独立子系统的 bug、互不依赖的重构任务。

**不适用：** 故障之间有关联、需要全局理解、涉及共享状态的场景。

#### requesting-code-review — 请求代码审查

**何时触发：** 每个任务完成后自动触发；实现重要功能后；合并到主分支前。

**做什么：**
- 获取变更的 git SHA 范围
- 派出代码审查子代理，提供完整的上下文（实现了什么、需求是什么、变更范围）
- 按严重程度分级报告问题：
  - **Critical（严重）** — 必须立即修复
  - **Important（重要）** — 继续下一个任务前必须修复
  - **Minor（次要）** — 记录下来，后续处理

#### receiving-code-review — 接收代码审查

**何时触发：** 收到代码审查反馈时，在实施建议之前自动触发。

**做什么：**
- 完整阅读反馈，用自己的话复述需求
- 对照代码库实际情况验证反馈的准确性
- 评估技术合理性，而不是无脑同意
- 一次只改一项，每项改完都跑测试

**核心原则：** 技术评估，而非表演性附和。如果审查意见在当前代码库中不成立，应当有理有据地反驳，而不是盲目实施。

**禁止出现：** "你说得太对了！"、不加思考的全盘接受、不验证就实施。

#### finishing-a-development-branch — 完成开发分支

**何时触发：** 所有任务完成，测试全部通过时。

**做什么：**
- 验证测试确实全部通过（必须成功才能继续）
- 检测主分支名称（main/master）
- 提供 4 个选项让你选择：
  1. **本地合并** — 拉取最新主分支，合并，清理工作区
  2. **推送并创建 PR** — 推送到远程，用 `gh` 创建 Pull Request
  3. **保留分支** — 不做任何操作，工作区保留
  4. **丢弃** — 需要输入 "discard" 确认，删除分支和工作区

---

### 元技能

#### using-superpowers — 技能系统入口

**何时触发：** 每次对话开始时自动加载。

**做什么：**
- 在回复任何消息之前，检查是否有相关技能可以触发
- 只要有 1% 的可能性某个技能适用，就必须先调用它
- 确定技能优先级：流程技能优先（brainstorming、debugging），实现技能其次

**你不需要关心这个技能。** 它是技能系统的引导程序，确保其他技能在正确的时机被触发。

#### writing-skills — 编写新技能

**何时触发：** 创建新技能、编辑现有技能时。

**做什么：**
- 将 TDD 思想应用于流程文档：先在没有技能的情况下测试基线行为（RED），写技能解决发现的问题（GREEN），封堵漏洞（REFACTOR）
- 确保技能的触发描述精确匹配使用场景
- 技能必须自包含，不依赖外部上下文

**适用人群：** 如果你想为团队定制特定场景的工作流（比如「数据库迁移检查清单」、「API 设计规范」），可以用这个技能来创建。

---

## 设计理念

- **测试驱动开发** — 永远先写测试
- **系统化优于即兴** — 流程优于猜测
- **降低复杂度** — 简单是首要目标
- **证据优于声明** — 验证后再宣布成功

## 更新

插件更新后技能会自动同步：

```bash
# Claude Code
/plugin update superpowers

# OpenCode
cd ~/.config/opencode/superpowers && git pull
# 重启 OpenCode
```

## 许可证

MIT License — 详见 LICENSE 文件。
