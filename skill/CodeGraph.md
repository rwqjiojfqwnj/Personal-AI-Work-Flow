# CodeGraph 小白使用指南

> 让 AI 编程助手「看懂」你的代码结构，省 token、省时间、少犯错

* * *

## 一、CodeGraph 是什么？

**一句话总结：** CodeGraph 是一个代码知识图谱工具，它把你的源代码解析成一棵"关系树"（谁调用了谁、谁继承了谁、哪个 URL 对应哪个方法），然后让 AI 编程助手直接查询这棵树，而不是傻傻地一个文件一个文件去 grep。

**类比理解：**

  * 没有 CodeGraph 的 AI = 一个新人入职，靠 `Ctrl+F` 翻文件找代码
  * 有 CodeGraph 的 AI = 一个有完整组织架构图的老员工，直接知道找谁



* * *

## 二、解决了什么问题？

### 痛点场景

场景 | 没有 CodeGraph | 有 CodeGraph  
---|---|---  
"这个接口被谁调用了？" | AI 用 grep 扫几百个文件，慢且可能漏 | 一次查询，秒出结果  
"改这个函数会影响哪些地方？" | AI 靠猜 + 反复搜索 | 直接给出影响范围（impact analysis）  
"这个项目的整体架构是什么？" | AI 读几十个文件拼凑答案 | 从知识图谱直接提取结构  
"这个 URL 对应哪个 Controller？" | AI 搜注解、搜路由文件 | 框架路由自动识别，直接关联  
  
### 量化收益（官方基准测试）

  * **省 16% 费用** （token 消耗减少 47%~64%）
  * **少 58% 工具调用** （AI 不用反复 grep/read）
  * **快 22% 响应** （直接查图 vs 扫文件）
  * **100% 本地运行** ，代码不上传，不需要 API Key



* * *

## 三、支持什么？

### 编程语言（20+）

Java、TypeScript、JavaScript、Python、Go、Rust、C#、PHP、Ruby、C、C++、Swift、Kotlin、Dart、Scala、Vue、Svelte 等

### AI 编程助手

  * ✅ **Hermes Agent**
  * ✅ **Claude Code**
  * ✅ **Codex CLI** （OpenAI）
  * ✅ **Cursor**
  * ✅ **OpenCode**
  * ✅ **Gemini CLI**
  * ✅ **Kiro**
  * ✅ **Antigravity IDE**



### 框架路由识别

Spring Boot（`@GetMapping`等）、Express、NestJS、Django、Flask、FastAPI、Laravel、Rails、Gin 等 14+ 框架

* * *

## 四、安装（3 步搞定）

### 第 1 步：安装 CLI 工具
    
    
    # 方式一：一键安装脚本（推荐，不需要 Node.js）
    # macOS / Linux
    curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh
    
    # Windows PowerShell
    irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex
    
    # 方式二：用 npm 安装（需要 Node.js）
    npm i -g @colbymchenry/codegraph
    

> ⚠️ 安装完后 **打开新终端** ，确保 `codegraph` 命令可用。验证：`codegraph --version`

### 第 2 步：连接你的 AI 助手
    
    
    codegraph install
    

这个命令会：

  * 自动检测你装了哪些 AI 助手
  * 把 CodeGraph 的 MCP 服务配置写入对应助手的配置文件
  * 设置自动允许权限（Claude Code）



> 也可以指定助手：`codegraph install --target=hermes,claude --yes`

### 第 3 步：初始化项目
    
    
    cd 你的项目目录
    codegraph init -i
    

`-i` 表示初始化 + 立即建索引。建完后项目下会多一个 `.codegraph/` 目录。

> 💡 大项目首次索引可能需要几分钟，后续增量同步很快。

* * *

## 五、日常使用

### 你不需要手动做任何事！

初始化完成后， **重启你的 AI 助手** （Claude Code / Hermes Agent 等），CodeGraph 就自动生效了。

AI 助手会自动使用 CodeGraph 的工具来回答你的问题。你只需要正常提问：
    
    
    "OrderService.createOrder() 被哪些地方调用了？"
    "修改 PaymentController 会影响哪些测试？"
    "这个项目的请求处理流程是怎样的？"
    "UserService 依赖了哪些其他服务？"
    

### CLI 命令速查
    
    
    codegraph status              # 查看索引状态
    codegraph sync                # 手动同步（一般不需要，自动的）
    codegraph query UserService   # 搜索符号
    codegraph callers createOrder # 查看谁调用了 createOrder
    codegraph callees createOrder # 查看 createOrder 调用了谁
    codegraph impact UserService  # 改 UserService 会影响什么
    codegraph files               # 查看索引的文件结构
    codegraph affected src/a.java src/b.java  # 改了这些文件，哪些测试受影响
    

* * *

## 六、优雅用法

### 1\. 改代码前先做影响分析
    
    
    你：我要重构 OrderService 的 calculatePrice 方法，帮我分析影响范围
    AI：（自动调用 codegraph_impact）→ 列出所有调用链、受影响的 Controller、测试文件
    

**好处：** 改之前就知道会波及哪些地方，不会改一处炸十处。

### 2\. 快速理解陌生代码库
    
    
    你：这个项目的订单支付流程是怎样的？从 Controller 到数据库
    AI：（自动调用 codegraph_explore）→ 给出完整的调用链路图
    

**好处：** 新人入职、接手别人代码，5 分钟搞懂核心流程。

### 3\. CI/CD 中只跑受影响的测试
    
    
    # 在 CI 脚本中
    AFFECTED=$(git diff --name-only HEAD | codegraph affected --stdin --quiet)
    if [ -n "$AFFECTED" ]; then
      mvn test -Dtest=$AFFECTED
    fi
    

**好处：** 大项目不用跑全量测试，只跑受改动影响的，CI 快 10 倍。

### 4\. 配合 Git 做 Code Review
    
    
    你：帮我 review 这次 PR 改的文件，看看有没有遗漏的调用方
    AI：（codegraph_callers + codegraph_impact）→ 找出所有可能受影响但没改的地方
    

### 5\. 微服务项目间追踪

对于 Spring Cloud 微服务，每个服务单独 `codegraph init`，然后：
    
    
    你：order-service 的 FeignClient 调用了 member-service 的哪些接口？
    AI：（通过路由识别 + 调用链分析）→ 列出跨服务依赖
    

* * *

## 七、配合 Hermes Agent / Claude Code / Codex 使用

### Hermes Agent

CodeGraph 已内置支持 Hermes Agent。运行 `codegraph install` 时会自动配置。

配置完成后，Hermes Agent 在回答代码问题时会自动调用 CodeGraph 的 MCP 工具：

  * `codegraph_explore` — 探索代码结构
  * `codegraph_search` — 搜索符号
  * `codegraph_callers` / `codegraph_callees` — 调用链分析
  * `codegraph_impact` — 影响分析



**你不需要做任何额外操作** ，正常和 Hermes Agent 对话即可。

### Claude Code
    
    
    # 安装时选择 claude
    codegraph install --target=claude --yes
    
    # 或手动配置 ~/.claude.json
    {
      "mcpServers": {
        "codegraph": {
          "type": "stdio",
          "command": "codegraph",
          "args": ["serve", "--mcp"]
        }
      }
    }
    

Claude Code 会自动使用 CodeGraph 来回答架构问题，减少 Explore agent 的 token 消耗。

### Codex CLI (OpenAI)
    
    
    codegraph install --target=codex --yes
    

Codex 会通过 MCP 协议连接 CodeGraph，在探索代码时直接查图。

### 通用配置（任何支持 MCP 的助手）
    
    
    {
      "mcpServers": {
        "codegraph": {
          "type": "stdio",
          "command": "codegraph",
          "args": ["serve", "--mcp"]
        }
      }
    }
    

* * *

## 八、常见问题

### Q: 索引很慢怎么办？

  * 确保 `node_modules`、`target`、`dist` 等目录被排除（默认已排除）
  * 大项目首次索引可能需要 5-10 分钟，后续增量同步很快
  * ⚠️ **WSL 用户注意** ：如果项目在 `/mnt/` (NTFS) 下，I/O 会很慢。建议把项目放到 WSL 原生文件系统（如 `~/projects/`）



### Q: 需要每次都手动 sync 吗？

不需要。CodeGraph 有文件监听器（inotify/FSEvents），你保存文件后 2 秒自动同步。

### Q: 支持中文注释/变量名吗？

支持。CodeGraph 解析的是代码结构（函数名、类名、调用关系），跟注释语言无关。

### Q: 数据安全吗？

100% 本地运行。数据存在项目目录的 `.codegraph/codegraph.db`（SQLite），不上传任何东西。

### Q: 卸载怎么弄？
    
    
    codegraph uninstall    # 从所有 AI 助手中移除配置
    codegraph uninit       # 删除某个项目的索引（在项目目录下运行）
    

* * *

## 九、工作原理（简单版）
    
    
    你的代码 (.java/.ts/.py...)
            ↓
       tree-sitter 解析（语法树）
            ↓
       提取节点（函数、类、方法）和边（调用、继承、导入）
            ↓
       存入 SQLite 数据库（.codegraph/codegraph.db）
            ↓
       AI 助手通过 MCP 协议查询
            ↓
       秒级返回：调用链、影响范围、代码结构
    

* * *

## 十、快速上手清单

  * 安装 CodeGraph CLI（`curl ... | sh` 或 `npm i -g`）
  * 运行 `codegraph install` 连接你的 AI 助手
  * 在项目目录运行 `codegraph init -i`
  * 重启 AI 助手
  * 正常提问，CodeGraph 自动生效



* * *

**项目地址：** [https://github.com/colbymchenry/codegraph](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fcolbymchenry%2Fcodegraph)  
**文档网站：** [https://colbymchenry.github.io/codegraph/](https://links.jianshu.com/go?to=https%3A%2F%2Fcolbymchenry.github.io%2Fcodegraph%2F)  
**版本：** v0.9.9  
**协议：** MIT（完全免费开源）
