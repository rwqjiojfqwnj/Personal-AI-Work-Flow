> 适合人群：正在使用或准备使用 AI 编程工具（Claude Code、OpenCode、Cursor 等）的开发者，想让 AI 编得更靠谱、干活更有章法的同学。

---

## 为什么你需要了解它？（Why）

### AI 编程的"裸奔"困境

你是不是也遇到过这样的场景：

打开 AI 编程助手，满怀期待地说："帮我实现一个用户登录功能。"AI 刷刷刷就给你搞出一版代码，你一看——嗯，能跑。但再一看：没有测试、没有设计文档、直接硬编码了数据库查询、错误处理就一个 `try-catch` 包全场……

三天后需求变了，你打开代码准备改，发现已经看不懂了。😭

这不是 AI 不够聪明，而是 AI **没有方法论**。它就像一个技术能力拉满但缺乏工程素养的实习生——你让它写代码它就写，但从不会主动问你"需求确认了吗？""要不要先写个测试？""设计方案讨论过了吗？"

### 更深层的问题

问题其实有两个层面：

1. **单次对话没有工程纪律**：AI 不会主动执行 TDD、不会先做设计再编码、不会交叉审查
2. **跨对话没有知识延续**：每次开新对话，AI 都是一张白纸，不知道你的项目规范、架构偏好、团队约定

有人尝试用超长的 System Prompt 来解决，有人每次都复制粘贴一堆规范……但这些方案要么撑爆上下文窗口，要么靠人肉记忆，终究不是长久之计。

### Superpowers 的解法

[Superpowers](https://github.com/obra/superpowers "Superpowers")（⭐ 178k）正是为解决这个问题而生。它不是又一个 AI 工具，而是一套**完整的 AI 编程方法论 + 可组合的技能框架**。它把资深工程师的工作习惯——先设计再编码、测试驱动开发、代码审查、系统化调试——编码成了一套可复用的"技能包"，让你的 AI 助手从"能写代码"升级为"会做工程"。

一句话总结：**Superpowers 给你的 AI 编程助手装上了软件工程的大脑。**

---

## 它到底是什么？（What）

### 核心概念拆解

Superpowers 本质上是两样东西的结合：

**1. 一套软件开发方法论**

它定义了 AI 辅助开发的完整工作流：
- 先通过**头脑风暴（brainstorming）**确认需求，而不是拿到任务就开写
- 把设计拆解成**详细的实施计划（writing-plans）**，每个任务 2-5 分钟粒度
- 强制执行 **TDD（测试驱动开发）**：先写测试 → 看它失败 → 写代码让它通过 → 重构
- 每完成一段代码，自动发起**代码审查（code-review）**
- 出了 bug 用**系统化调试（systematic-debugging）**，而不是瞎猜

**2. 一套可组合的技能包（Skills）**

每个方法论步骤都被封装成独立的"技能"，以 `SKILL.md` 文件的形式存在。技能系统的设计非常精巧：

```
skills/
├── brainstorming/
│   └── SKILL.md          # 头脑风暴技能
├── writing-plans/
│   └── SKILL.md          # 编写计划技能
├── test-driven-development/
│   └── SKILL.md          # TDD 技能
├── systematic-debugging/
│   └── SKILL.md          # 系统化调试技能
├── requesting-code-review/
│   └── SKILL.md          # 请求代码审查技能
├── subagent-driven-development/
│   └── SKILL.md          # 子智能体驱动开发技能
└── ...更多技能
```

每个 `SKILL.md` 的结构都是：

```markdown
---
name: brainstorming
description: 在写代码前，通过提问完善想法，探索替代方案，分段验证设计
---

# 头脑风暴

[具体的指令和工作流程...]
```

### 渐进式披露：不会撑爆你的上下文

你可能会担心：装了这么多技能，每次对话不会被塞得满满的吗？

不会。技能系统采用**渐进式披露（Progressive Disclosure）**策略：

| 层级 | 加载内容 | 时机 |
|------|---------|------|
| 第一层 | 只读取 `name` + `description`（几行文字） | 对话开始时 |
| 第二层 | 加载完整的 SKILL.md 指令 | AI 判断需要激活该技能时 |
| 第三层 | 加载辅助脚本、参考文档 | 技能指令中明确要求时 |

所以即使你安装了几十个技能，日常对话中只有极少量元数据在上下文里。需要时才加载，用完即走。

### 完整的工作流程

安装了 Superpowers 后，你的 AI 编程助手会自动按照这个流程工作：

```
你说"帮我做个功能"
    → AI 启动 brainstorming，反问你需求细节
    → 产出设计文档，分段给你确认
    → 你说"没问题，开始吧"
    → AI 启动 writing-plans，拆分成小任务
    → 启动 subagent-driven-development，逐个执行任务
    → 每个任务都走 TDD 流程
    → 每完成一段，自动 code-review
    → 全部完成后，整理分支，准备合并
```

是不是像极了一个靠谱的高级工程师的工作方式？没错，这就是 Superpowers 的核心价值——**把好的工程实践自动化**。

### 技能生态：不止 Superpowers

Superpowers 是最知名的技能框架，但其实整个 Agent Skills 生态已经非常繁荣：

- **[skills.sh](https://skills.sh "skills.sh")**：由 Vercel 运营的技能目录，已收录 **91,000+** 个技能
- **[npx skills](https://github.com/vercel-labs/skills "npx skills")**：Vercel 出品的技能 CLI，一行命令安装技能
- 热门技能包来自 Anthropic、Microsoft、Google、Vercel 等大厂

目前支持的 AI 编程工具超过 **50 种**，包括 Claude Code、OpenCode、Cursor、Codex、Gemini CLI、GitHub Copilot 等。

---

## 怎么用？（How）

下面以开源的 **OpenCode** 为例，演示完整的安装和使用流程。选择 OpenCode 是因为它完全开源、免费，而且对 Superpowers 的支持非常成熟。

### 前置准备：安装 OpenCode

OpenCode 是一个基于终端的 AI 编程工具，安装很简单：

```bash
# 方式一：npm 全局安装
npm install -g opencode-ai

# 方式二：使用安装脚本（推荐）
curl -fsSL https://opencode.ai/install | bash
```

安装后，在项目目录运行 `opencode` 即可启动 TUI 界面。

### 第一步：安装 Superpowers 插件

在你的 `opencode.json`（全局或项目级别）中添加：

```json
{
  "plugin": ["superpowers@git+https://github.com/obra/superpowers.git"]
}
```

全局配置文件位置：`~/.config/opencode/opencode.json`

然后重启 OpenCode，输入 "Tell me about your superpowers"，如果 AI 能列出可用技能，就说明安装成功了。🎉

> **Windows 用户注意**：部分 Windows 版本的 OpenCode 在处理 `git+https` 插件时可能遇到问题。如果安装失败，可以用以下替代方案：
>
> ```powershell
> npm install superpowers@git+https://github.com/obra/superpowers.git --prefix "$HOME\.config\opencode"
> ```
>
> 然后在 `opencode.json` 中改用本地路径：
>
> ```json
> {
>   "plugin": ["~/.config/opencode/node_modules/superpowers"]
> }
> ```

### 第二步：体验核心工作流

安装完成后，你不需要做任何额外配置。Superpowers 的技能会**自动激活**——AI 会根据对话内容自动判断应该使用哪个技能。

**场景一：从零开发一个功能**

```
你：帮我实现一个 Markdown 转 HTML 的 CLI 工具
```

如果没有 Superpowers，AI 会直接甩给你一堆代码。但有了 Superpowers，对话会变成这样：

```
AI（自动激活 brainstorming）：
  在开始编码之前，让我先理解几个问题：
  1. 你需要支持哪些 Markdown 扩展？（GFM？表格？脚注？）
  2. 输出格式有什么要求？（完整 HTML 文档还是 fragment？）
  3. 是否需要自定义模板支持？
  4. 目标用户是谁？（开发者自用还是给非技术用户？）

你：支持 GFM，输出 fragment 就好，开发者自用，不需要模板

AI（产出设计方案，分段展示）：
  ## 设计方案（第 1/3 段）
  ### 核心架构
  - 使用 markdown-it 作为解析引擎，启用 GFM 插件
  - CLI 参数：输入文件路径、输出路径（可选）、是否 watch 模式

  这个方向你觉得可以吗？

你：可以，继续

AI（自动切换到 writing-plans）：
  拆分为以下任务：
  Task 1: 项目初始化和依赖安装（2 min）
  Task 2: 核心转换函数 + 单元测试（5 min）
  Task 3: CLI 参数解析（3 min）
  Task 4: 集成测试和错误处理（3 min）

你：开始执行

AI（自动切换到 subagent-driven-development + TDD）：
  [执行 Task 1]
  [执行 Task 2] 先写测试 → 运行失败 → 写实现 → 测试通过 ✅
  [自动 code-review] 检查代码质量...
  ...
```

看到了吗？整个过程有章法、有节奏，像一个资深工程师在带着你做项目。

**场景二：调试一个诡异的 bug**

```
你：这个函数有时候返回 undefined，有时候正常，帮我看看
```

```
AI（自动激活 systematic-debugging）：
  让我按系统化调试的 4 个阶段来排查：

  阶段 1：证据收集
  - 请问 undefined 出现的频率大概多少？
  - 有没有特定的输入会必现？
  - 运行环境是什么？（Node 版本、操作系统）

  阶段 2：假设生成
  基于代码分析，我有以下假设：
  1. 竞态条件：异步操作的时序问题
  2. 缓存未命中：某些边界条件下缓存为空
  3. ...

  阶段 3：假设验证
  让我逐个排查...
```

对比一下没有 Superpowers 时 AI 的做法——直接看代码，瞎猜一个原因，改一改，不行再猜。高下立判。

### 第三步：使用 Skills CLI 安装更多技能

除了 Superpowers 自带的技能，你还可以从生态中安装更多：

```bash
# 安装 Vercel 出品的技能 CLI（推荐固定版本）
npm i -g skills@1.4.0

# 浏览某个技能包的所有技能
skills add anthropics/skills --list

# 安装特定技能
skills add anthropics/skills --skill frontend-design

# 安装 Vercel 的 React 最佳实践技能
skills add vercel-labs/agent-skills --skill vercel-react-best-practices

# 搜索技能
skills find "debugging"

# 安装到全局（所有项目可用）
skills add obra/superpowers -g
```

**推荐的技能包**：

| 技能包 | 来源 | 适合场景 |
|--------|------|---------|
| `anthropics/skills` | Anthropic 官方 | 前端设计、代码简化、PDF/文档处理 |
| `vercel-labs/agent-skills` | Vercel 官方 | React/Next.js 最佳实践 |
| `microsoft/azure-skills` | 微软 | Azure 云服务开发 |
| `obra/superpowers` | Superpowers 作者 | 完整开发方法论（TDD、调试、计划等） |
| `supabase/agent-skills` | Supabase | PostgreSQL 和 Supabase 开发 |
| `firebase/agent-skills` | Google | Firebase 开发 |

### 第四步：创建自己的技能

Superpowers 的技能格式非常简单，你完全可以创建自己的技能：

```bash
# OpenCode 的个人技能目录
mkdir -p ~/.config/opencode/skills/my-code-style
```

创建 `SKILL.md`：

```markdown
---
name: my-code-style
description: 当编写代码时使用此技能，确保遵循团队编码规范
---

# 团队编码规范

## 命名约定
- 变量和函数：camelCase
- 类和接口：PascalCase
- 常量：UPPER_SNAKE_CASE
- 文件名：kebab-case

## TypeScript 规则
- 始终使用 strict 模式
- 优先使用 interface 而非 type（除非需要联合类型）
- 禁止使用 any，用 unknown + 类型守卫
- 异步函数必须有错误处理

## 测试规则
- 测试文件与源文件同目录，命名为 `*.test.ts`
- 使用 describe/it 结构
- 每个测试只验证一个行为
```

保存后重启 OpenCode，这个技能就会被自动发现。当 AI 写代码时，它会自动激活你的编码规范技能。

**技能优先级**：项目级技能 > 个人技能 > Superpowers 技能。这意味着你可以在项目里覆盖全局规范。

---

## 常见误区与最佳实践

### ❌ 误区一：安装了 Superpowers 就万事大吉

Superpowers 是一套方法论，不是魔法。它需要你：
- **积极参与对话**：在 brainstorming 阶段认真回答 AI 的问题
- **审查 AI 的计划**：不要盲目 "LGTM"，真的读一读计划是否合理
- **敢于说"不"**：如果 AI 的方案有问题，直接说出来

### ❌ 误区二：一次安装几十个技能

技能虽然不会都加载到上下文，但过多的技能会增加 AI 选择判断的负担。建议：
- 核心方法论用 Superpowers（已经很全了）
- 技术栈相关的按需安装 1-2 个
- 团队规范写成自定义技能

### ❌ 误区三：忽略技能的触发条件

每个技能的 `description` 字段决定了它何时被激活。写自定义技能时，**描述要清晰具体**：

```markdown
# ❌ 不好的描述
description: 代码相关的技能

# ✅ 好的描述
description: 当编写或修改 React 组件时使用，确保遵循组件设计规范和性能最佳实践
```

### ✅ 最佳实践总结

1. **从 Superpowers 开始**：它的方法论技能是经过大量验证的，先用起来
2. **学会 TDD 节奏**：即使你之前不写测试，Superpowers 会"逼"你走 TDD，这是好事
3. **利用 brainstorming**：不要急着写代码，先让 AI 帮你想清楚需求
4. **渐进式丰富技能**：先用好核心技能，再按需添加
5. **团队统一技能配置**：把自定义技能放在项目的 `.opencode/skills/` 目录，随代码版本管理

---

## 跨工具通用：不止 OpenCode

Superpowers 支持几乎所有主流 AI 编程工具，安装方式各有不同：

| 工具 | 安装方式 |
|------|---------|
| **Claude Code** | `/plugin install superpowers@claude-plugins-official` |
| **OpenCode** | `opencode.json` 添加 plugin 配置 |
| **Codex CLI** | `/plugins` → 搜索 superpowers → 安装 |
| **Cursor** | `/add-plugin superpowers` |
| **Gemini CLI** | `gemini extensions install https://github.com/obra/superpowers` |
| **GitHub Copilot CLI** | 通过 marketplace 安装 |

技能文件格式是统一的，所以你写的自定义技能在不同工具间可以复用。

---

## 写在最后

AI 编程工具的竞争，早已不只是"谁的模型更聪明"。真正拉开差距的，是**谁能把好的工程实践系统化地注入到 AI 的工作流中**。

Superpowers 和整个 Agent Skills 生态做的就是这件事——它们不是在替你写代码，而是在教你的 AI 助手"**怎样才算把事情做对**"。

从今天开始，给你的 AI 编程助手装上超能力吧。你会发现，代码质量提升的不只是一点点。🚀

---

**相关链接**：
- Superpowers 仓库：https://github.com/obra/superpowers
- OpenCode 官网：https://opencode.ai
- Skills 技能目录：https://skills.sh
- Skills CLI 仓库：https://github.com/vercel-labs/skills
- OpenCode 技能文档：https://opencode.ai/docs/skills

[Superpowers 介绍与技能集使用实践：给你的 AI 编程助手装上超能力为什么你需要了解它？（Why） AI 编程 - 掘金](https://juejin.cn/post/7635681418753359912?searchId=202606281249302E911F7B612C1784E332)