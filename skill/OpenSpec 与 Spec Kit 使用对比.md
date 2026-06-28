[](https://juejin.cn/user/2524134428655159/posts)

2026-02-092,911阅读7分钟

> AI 编程工具越来越强，但"先写规范再写代码"的 SDD（Spec-Driven Development）理念也开始流行。目前最有代表性的两个开源框架是 GitHub 的 Spec Kit 和 Fission AI 的 OpenSpec，这篇文章梳理它们各自的用法和差异。

## 先说背景：为什么需要 SDD

用 Cursor、Claude Code 或 Copilot 写代码时，很多人习惯直接丢一段需求描述给 AI，然后看它输出什么。这种 "Vibe Coding" 在小功能上还行，一旦需求复杂一点，生成的代码往往似是而非 —— 不是模型不够强，是我们没给够上下文。

SDD 的做法很朴素：先把要做什么写成结构化的规范文档，再让 AI 照着规范写代码。规范本身也由 AI 辅助生成，但每一步人都可以审查和修正。两个框架对"怎么组织这个流程"给出了不同的答案。

## Spec Kit：GitHub 出品，流程严谨

#### 定位

Spec Kit（28K+ Star）不是 AI 助手，而是一套给 AI 助手"加规矩"的工具包。它往你项目里注入一组 slash commands，通过 GitHub Copilot、Claude Code、Gemini CLI 等工具调用。核心思路是门控流程 —— 每一步做完、人确认了，才能进下一步。

#### 安装

Python 生态，用 `uv` 装：
```
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
specify init my-project
```

`specify init` 跑完后，slash commands 就被写进项目的 agent 配置目录了（`.claude/`、`.github/prompts/` 等），后续不再需要 `specify` 命令。

#### 工作流：7 步门控

`Constitution → Specify → Clarify → Plan → Tasks → Analyze → Implement`

逐步说明：

**`/speckit.constitution` — 定规矩**

创建 `constitution.md`，写死项目的不可违反原则：测试覆盖率底线、技术栈约束、编码规范等。AI 在后续每一步都会参考这份文件，减少"上下文漂移"。

**`/speckit.specify` — 写规范**

用自然语言描述要做什么、为什么做，不需要涉及技术细节。

**`/speckit.clarify` — 消歧**

AI 基于规范抛出一系列结构化问题，覆盖潜在的歧义点，答案回写到规范的 Clarifications 部分。这步挺有价值 —— 很多返工就是因为前期没把需求想清楚。

**`/speckit.plan` — 技术方案**

你给出技术方向（用什么框架、什么架构），AI 生成详细方案。

**`/speckit.tasks` — 任务拆解**

把规范和方案拆成可执行的任务列表，带依赖顺序、并行标记（`[P]`）、文件路径和测试要求。

**`/speckit.analyze` — 一致性检查**

交叉检查规范、方案和任务之间有没有矛盾。输出一个 Findings Table，每行有编号（A1、C2 之类），你可以直接说"Fix A1, C2"让 AI 去改。

**`/speckit.implement` — 执行**

基于所有产物开始写代码。

#### 优缺点

好处是严谨。每步都有人工卡点，clarify 和 analyze 能有效减少返工。

代价是重。specify 阶段就能产出 800 多行文档，整套流程 8 个 slash command，跑一遍下来需要不少耐心。对日常小功能迭代来说有些 overkill。

## OpenSpec：轻量灵活，为存量代码而生

#### 定位

OpenSpec的设计出发点不太一样 —— 它假设你大部分时间是在已有代码库上做增量变更，而非从零开始。所以它围绕"变更（Change）"这个概念来组织一切。

v1.0.0（The OPSX Release）是个大版本，把早期的 3 步线性流程重构成了 10 个可自由组合的 OPSX 命令，不再强制按顺序走。

#### 安装

TypeScript 写的，需要 Node.js 20.19.0+：

```
npm install -g @fission-ai/openspec
openspec init
# 或直接指定工具 openspec
init --tools claude,cursor
```

目前支持多种AI工具（Claude Code、Cursor、Windsurf、Gemini CLI、Copilot、Cline、Amazon Q 等）。初始化后在项目里生成对应 的skill 文件，不同工具的命令语法略有差异：

|工具|格式|
|---|---|
|Claude Code|`/opsx:command`|
|Cursor / Windsurf / Copilot|`/opsx-command`|
|Trae|`/openspec-command-name`|

#### 核心概念：产物依赖图

理解 OpenSpec 的关键是它的产物依赖图（Artifact Graph）。每个变更包含 4 个产物：

```
proposal (根)
 ├── specs    (依赖 proposal)
 ├── design   (依赖 proposal)
 └── tasks    (依赖 specs + design)
```

每个产物有三种状态：BLOCKED → READY → DONE。AI 通过查询 CLI 拿到实时状态，自动判断该做什么。这意味着你可以随时回去改任何产物，系统会自动更新状态链路。

#### 完整命令（10 个）

按使用阶段分：

**探索**

`/opsx:explore` — 在正式开始前调研问题、对比方案。这个模式有硬隔离，不会产生任何产物或代码变更，纯粹用来想清楚。

**规划**

`/opsx:new <name>` — 创建一个变更，在 `openspec/changes/<name>/` 下搭好脚手架。支持 `--schema` 参数指定自定义产物流程。

`/opsx:ff` — Fast-Forward，按依赖顺序一口气生成 proposal、specs、design、tasks 四个产物。适合需求明确的场景。

`/opsx:continue` — 每次只生成下一个就绪的产物，适合边想边做。

> `ff` 还是 `continue`？想清楚了用 `ff`，没想清楚用 `continue`。

**实现**

`/opsx:apply` — 按照 specs 和 design 逐个实现 tasks.md 里的任务。支持 `/opsx:apply <name>` 指定变更名，做到断点续跑。

`/opsx:verify` — 实现完了跑一遍校验，检查代码和规范是否一致。问题分三级：CRITICAL / WARNING / SUGGESTION。

**归档**

`/opsx:sync` — 把变更里的 delta specs 合并回主规范目录。可选步骤，archive 时也会提示。

`/opsx:archive` — 验证产物和任务完成后，把变更移到 archive 目录。

`/opsx:bulk-archive` — 同时归档多个变更，自动检测跨变更的规范冲突。

**入门**

`/opsx:onboard` — 用你的真实代码库做一次完整的引导教学，11 个阶段，大约 15 分钟。

#### 三种典型工作流

|模式|场景|流程|
|---|---|---|
|快速功能|需求清晰|`new → ff → apply → verify → archive`|
|探索式|需求模糊|`explore → new → continue → ... → apply → verify → archive`|
|并行变更|多任务并行|多个 `new`，按需切换，最后 `bulk-archive`|

#### 几个值得注意的设计

**变更隔离**

OpenSpec 把"当前系统状态"和"变更提案"放在不同目录：

`openspec/ ├── specs/          ← 系统当前状态（Source of Truth） ├── schemas/        ← 自定义产物工作流 ├── config.yaml     ← 项目配置 └── changes/        ← 各个变更提案     ├── add-dark-mode/     ├── fix-login-bug/     └── archive/    ← 历史变更`

每个功能在独立的 change 目录里开发，只在 archive 时才合并到主规范。多人并行开发时不会互相踩。Spec Kit 没有这层隔离，直接改主规范文件，协作时容易冲突。

**Delta Spec**

变更中的规范用 `ADDED`、`MODIFIED`、`REMOVED`、`RENAMED` 标记增量，不是全量覆盖。看一眼就知道这次改了什么。

**动态指令（v1.0 新增）**

AI 拿到的指令不再是写死的模板，而是从三层动态拼装：项目上下文（`config.yaml`）、产物规则、输出模板。AI 每次操作前都会查 CLI 获取最新状态，所以指令始终跟项目实际情况同步。

#### CLI 命令

除了 AI 助手里的 slash commands，终端里也有一组 CLI 命令：

|命令|用途|
|---|---|
|`openspec init`|初始化项目|
|`openspec status`|查看产物完成状态|
|`openspec list`|列出活跃变更和规范|
|`openspec show <name>`|查看变更详情|
|`openspec view`|交互式仪表盘|
|`openspec validate`|校验规范格式|
|`openspec archive`|终端归档（`--yes` 跳过确认）|
|`openspec feedback`|提交反馈|
|`openspec completion install`|安装 Shell 补全|

## 对比：怎么选

#### 核心差异

|维度|Spec Kit|OpenSpec|
|---|---|---|
|出品方|GitHub|Fission AI|
|语言 / 安装|Python / `uv`|TypeScript / `npm`|
|Stars|28K+|22K+|
|设计思路|严格门控，步步审查|依赖图驱动，灵活迭代|
|擅长场景|新项目从零开始（Greenfield）|已有代码库增量开发（Brownfield）|
|命令数量|8 个|10 个|
|规范体量|较重，单阶段可达 800+ 行|较轻，文档约 250 行|
|流程自由度|线性，不能跳步|非线性，随时可以回改|
|协作友好度|直接改主规范，多人易冲突|变更隔离，archive 时合并|
|Token 消耗|较高|较低|
|变更追踪|无内建机制|Delta Spec + 归档历史|

快速上手命令对照：

**Spec Kit：**

`uv tool install specify-cli --from git+https://github.com/github/spec-kit.git specify init my-project # 在 AI 助手中： /speckit.specify → /speckit.clarify → /speckit.plan → /speckit.tasks → /speckit.implement`

**OpenSpec：**

`npm install -g @fission-ai/openspec openspec init --tools claude # 在 AI 助手中： /opsx:new feature-name → /opsx:ff → /opsx:apply → /opsx:verify → /opsx:archive`

#### 选择建议

|场景|推荐|
|---|---|
|新项目，要求严谨规范|Spec Kit|
|已有代码库做功能迭代|OpenSpec|
|多人协作，关注冲突管理|OpenSpec|
|合规性 / 可审计性要求高|Spec Kit|
|追求开发速度|OpenSpec|
|需求模糊，需要深度消歧|Spec Kit|

## 总结

两者不是非此即彼。一种可行的组合方式：新项目启动阶段用 Spec Kit 把架构和规范敲定，进入日常迭代后切到 OpenSpec 做增量变更管理。