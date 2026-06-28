# AI 全栈开发工作流（SDD 模式）— 通用版 v3

> **适用场景**：Vue 3 + Spring Boot 前后端分离，一人全栈
> **核心理念**：Codegraph 建立认知 → Comet 驱动流程 → 契约先行 → AI 执行你来审

---

## 阶段 0：项目初始化（代码拉下来第一件事）

### 0.1 脚手架搭建

```bash
# 前端
npm create vue@latest frontend

# 后端
# 用 Spring Initializr / IDEA 创建
# 依赖：spring-boot-starter-web, mybatis-plus, redis, knife4j, lombok
```

### 0.2 建立 AI 知识图谱（必须第一步做）

代码拉下来 / 脚手架搭好后，**立即执行**：

```
/understand --language zh --full
```

这一步会让 AI 扫描整个项目，生成 `.understand-anything/knowledge-graph.json`。之后 AI 讨论任何代码问题时，都能基于这个图谱理解你的项目结构、依赖关系、架构分层。

**这步不做的后果**：后续 AI 给你的建议都是泛泛而谈，不知道你的实际项目长什么样。

### 0.3 建立文档目录

```
project/
├── docs/
│   ├── prd/              # 产品给的 PRD 放这里
│   ├── api-external/     # 三方接口文档放这里
│   ├── specs/            # AI 生成的规格文档
│   └── design/           # AI 生成的设计文档
├── frontend/             # Vue 3
├── backend/              # Spring Boot
├── .understand-anything/ # Codegraph 知识图谱（不要删）
└── .codebuddy/           # AI 工作流数据（不要删）
```

### 0.4 初始化标准提示词

```
我刚搭建好了项目脚手架：
- 前端：Vue 3 + TypeScript，目录 frontend/
- 后端：Spring Boot + Java + MyBatis-Plus，目录 backend/

请帮我完成初始化：

1. 用 /understand --language zh --full 建立项目知识图谱
2. 建立 docs/ 标准文档目录结构（prd/、api-external/、specs/、design/）
3. 确认项目基础配置正确（前后端能正常启动）
```

### 0.5 知识图谱的日常维护

```
# 每次大改完代码后增量更新
/understand --auto-update

# 可视化查看项目架构
/understand-dashboard

# 深入理解某个模块
/understand-explain frontend/src/views/xxx.vue

# 分析某个 PR / diff 的影响范围
/understand-diff
```

---

## 阶段 1：需求探索与澄清

**触发**：`/comet-open`

**前置条件**：Codegraph 已建立，PRD 文档已放入 `docs/prd/`，三方接口文档已放入 `docs/api-external/`

### 1.1 完整需求探索（标准模板）

```
我正在开发一个新功能：[一句话描述]

相关材料：
- PRD 文档：docs/prd/xxx.md
- 三方接口文档：docs/api-external/xxx.md
- 项目知识图谱：.understand-anything/knowledge-graph.json（已用 codegraph 建立）

请按照 Comet Open 流程进行需求探索：

第一轮 — 完整性分析：
- 逐条分析 PRD 中每个功能点，评估需求描述是否足够进入开发
- 标记：可直接设计 / 需澄清 / 不建议现阶段启动

第二轮 — 接口对齐：
- 逐一对照三方接口文档，输出接口匹配度矩阵
- 明确：有现成接口支撑 / 可曲线实现（说明路径）/ 完全无接口

第三轮 — 缺失项与冲突项：
- PRD 内部逻辑冲突（如不同章节对同一事物的描述矛盾）
- PRD 与接口文档的能力冲突
- 关键缺失项（权限模型、会话策略、异步任务机制等）

第四轮 — 风险清单：
- 按高风险 / 中风险 / 技术债三级输出
- 每个风险说明影响范围和缓解建议

输出：
1. 需求澄清清单
2. 本期范围收敛建议（最小闭环 + 后续增强分阶段）
3. 需与产品确认的问题清单（分三级：必须 / 建议 / 可逐步）
4. 需与接口方确认的问题清单
```

### 1.2 只有 PRD，无三方接口

```
PRD：docs/prd/xxx.md，暂无三方接口文档。
项目知识图谱已建立。

请做需求澄清：
1. 评估 PRD 完整性，找出模糊、缺失、前后矛盾处
2. 列出需向产品确认的问题，按优先级分三级
3. 给出本期最小可交付范围建议
4. 标注哪些能力需要外部接口支撑，提醒后续补充接口文档
```

### 1.3 只做技术可行性评估

```
PRD：docs/prd/xxx.md
接口文档：docs/api-external/xxx.md

请结合项目现有代码（codegraph 知识图谱），评估技术可行性：
1. 现有代码中有哪些模块可以复用
2. 哪些功能可以直接用三方接口实现
3. 哪些需要组合/封装三方接口（说明路径和风险）
4. 哪些完全需要自建
5. 三方接口的限流、认证、错误码是否足够支撑
6. 给出最终判断：可行 / 需澄清 / 暂不可行
```

### 1.4 产出物

| 产出 | 用途 |
|------|------|
| 需求澄清清单 | 评审会上与产品对齐 |
| 接口匹配度矩阵 | 与接口方沟通的依据 |
| 范围收敛建议 | 排期输入 |
| 问题清单 | 分发给产品和接口方 |

---

## 阶段 2：深度设计

**触发**：`/comet-design`

**前置条件**：需求已澄清，本期范围已确认

### 2.1 标准设计提示词

```
需求已澄清，本期范围已确认。请进入 Comet Design 深度设计。

技术栈：Vue 3 + TypeScript，Spring Boot + Java + MyBatis-Plus，前后端分离，RESTful API。
项目代码结构参考 codegraph 知识图谱。

请按以下顺序产出设计文档，每步让我确认后再继续：

第一步 — 数据库设计：
- 完整表结构（字段名、类型、长度、必填、默认值、注释）
- 索引设计（类型、字段、理由）
- 如果有缓存需求，说明缓存策略

第二步 — API 接口设计：
- 每个接口：方法 + 路径 + 请求参数 + 响应结构 + 错误码
- 统一返回格式规范
- 分页/排序/筛选参数规范
- 异步任务的状态查询接口设计

第三步 — 前后端类型契约（最关键，SDD 核心）：
- TypeScript：interface / enum / type 完整定义，写入 frontend/src/types/
- Java：DTO / VO / Enum 完整定义，写入 backend/.../dto/
- 两端字段名、类型、枚举值必须一一对应

第四步 — 前端设计：
- 路由表
- 页面组件树与拆分方案
- Pinia Store 设计
- 公共组件复用策略

第五步 — 后端设计：
- Controller → Service → Mapper 分层职责
- 异常处理策略
- 拦截器/过滤器使用场景
```

### 2.2 注入非功能性约束（关键！）

**AI 默认不会主动做性能优化，你必须在设计阶段就告诉它。**

```
设计时需要遵守以下约束：

【后端性能约束】
- 禁止循环中执行 SQL，批量操作使用 saveBatch / updateBatchByIds
- 列表查询必须分页，pageSize 上限 100
- 超过 3 张表的关联查询，拆分为多次查询在 Service 层组合
- TEXT/LONGTEXT 字段不参与列表查询，仅详情接口返回
- 字典/配置/分类树等低频变更数据，用 Redis 缓存
- 缓存采用旁路缓存模式：先更新数据库，再删除缓存
- 禁止无过期时间的缓存
- 所有接口参数做 @Valid 校验
- 长时间任务必须异步，返回任务 ID 供前端轮询
- 调用三方接口记录请求和响应日志
- 异常日志包含完整堆栈

【前端性能约束】
- 列表必须分页加载，不做全量拉取
- 搜索输入做 300ms 防抖
- 相同接口并发请求去重
- 长列表用虚拟滚动
- 大表单 Tab 场景用 v-if 按需渲染
- 图片列表懒加载
- 所有异步操作有 loading 和 error 状态
- 表单提交按钮防重复点击
- 页面切换时未保存内容给离开确认

【前端 UI 约束】
- 组件库：[Element Plus / Ant Design Vue / Naive UI 三选一]
- 风格：简洁专业，间距 16px/24px，圆角 8px，合理留白
- 仅适配桌面端（≥1280px），不做移动端适配
```

### 2.3 产出物

| 产出 | 位置 |
|------|------|
| 数据库设计文档 | docs/design/database.md |
| API 设计文档 | docs/design/api.md |
| 前端类型定义 | frontend/src/types/ |
| 后端 DTO | backend/.../dto/ |
| 架构设计文档 | docs/design/architecture.md |

---

## 阶段 3：计划与构建

**触发**：`/comet-build`

**前置条件**：设计文档已确认，类型契约已写入代码

### 3.1 任务拆分

```
设计文档已确认。请基于设计文档和 codegraph 知识图谱，将开发任务拆分为可执行清单。

要求：
1. 按依赖关系排序
2. 标注每个任务的完成标准
3. 标注可并行执行的任务组
4. 标注需要修改的现有代码（参考知识图谱中的依赖关系）
```

### 3.2 选择开发模式

#### 模式 A：后端先出骨架 + 前端并行 Mock（推荐）

```
请先完成所有后端 Controller 骨架代码：
- 完整的 Knife4j/Swagger 注解（含请求参数、响应示例）
- @Valid 参数校验注解
- 统一的 ApiResponse 返回格式
- 暂时返回 Mock 数据

完成后，前后端并行开发：
- 前端基于 Swagger 文档 + 类型契约独立开发
- 后端逐个接口实现真实逻辑

后端开发遵守：[粘贴后端性能约束]
前端开发遵守：[粘贴前端性能约束]
```

#### 模式 B：Team Mode 全并行（有经验后使用）

```
前后端设计已对齐，类型契约已写入代码。
请使用 Team Mode：

- 创建 frontend-dev：负责所有前端代码
- 创建 backend-dev：负责所有后端代码
- 两成员基于类型契约独立开发，通过 send_message 协调变更

后端约束：[粘贴后端性能约束]
前端约束：[粘贴前端性能约束]
```

### 3.3 前端 Mock 策略（后端未就绪时）

```
后端接口尚未就绪，请帮我搭建前端 Mock：

方案：使用 vite-plugin-mock
1. 基于 frontend/src/types/ 中的类型定义生成 Mock 数据
2. 覆盖正常响应 + 空数据 + 错误响应
3. 保留环境变量开关 VITE_USE_MOCK，后端就绪后一键切换
```

### 3.4 TDD 开发（关键业务逻辑）

```
请用 TDD 模式实现 [具体功能]：
1. 先写测试用例
2. 再写实现代码
3. 运行测试确认通过
4. 失败则分析原因后修复
```

### 3.5 单模块标准提示词

```
请实现 [模块名称]：

后端：
- Controller 带完整 Swagger 注解 + @Valid 校验
- Service 层处理业务逻辑和异常
- Mapper 用 MyBatis-Plus，复杂查询写 XML
- 返回统一 ApiResponse

前端：
- src/api/ 封装接口调用
- src/types/ 定义接口类型（如已存在则复用）
- 页面组件合理拆分，公共部分提取
- 包含 loading / 空状态 / 错误状态处理

完成后自己审查代码，然后让我确认。
```

---

## 阶段 4：验证与收尾

**触发**：`/comet-verify`

### 4.1 自动化验证

```
开发已完成，请进行 Comet Verify 验证：

1. 契约一致性检查
   - 对比 frontend/src/types/ 和 backend/.../dto/
   - 检查字段名、类型、枚举值是否完全一致
   - 检查 API 路径和方法是否匹配

2. 后端检查
   - mvn test 是否通过
   - 是否有未处理的 TODO
   - 异常处理是否完整

3. 前端检查
   - npm run lint 是否通过
   - npm run build 是否成功
   - 是否有硬编码的接口地址

4. 对照设计文档逐项确认功能完整性

请输出验证报告。
```

### 4.2 联调指导

```
前后端都在本地运行。请指导联调：
1. 确认后端 Swagger 文档可访问
2. 前端代理配置是否正确
3. 按接口列表逐一测试
4. 遇到问题按优先级排查：URL → 参数格式 → 跨域 → 后端响应
```

### 4.3 验证后更新知识图谱

```
验证通过。请更新项目知识图谱：
/understand --auto-update
```

---

## 阶段 5：归档

**触发**：`/comet-archive`

```
功能已验证通过。请执行归档：

1. 将设计文档合并到 docs/specs/
2. 更新项目知识图谱
3. 清理临时文件
4. 生成本次开发总结（做了什么、技术决策、遗留问题）
5. 引导 Git 分支收尾（合并/PR/清理）
```

---

## 附录 A：从零到一完整对话模板

以下是一条龙模板，复制替换 `[xxx]` 后直接发给 AI：

```
我的项目：
- 前端：Vue 3 + TypeScript + [Element Plus]，目录 frontend/
- 后端：Spring Boot + Java + MyBatis-Plus + Redis，目录 backend/
- PRD：docs/prd/[xxx].md
- 三方接口文档：docs/api-external/[xxx].md（无则删此行）

【后端性能约束】
- 禁止循环查数据库，批量操作用 saveBatch
- 列表必须分页，pageSize 上限 100
- 热点数据 Redis 缓存，旁路缓存模式
- 禁止无过期时间的缓存
- 所有接口参数 @Valid 校验
- 长时间任务异步处理，返回任务 ID
- 调用三方接口记录请求响应日志

【前端性能约束】
- 列表分页加载
- 搜索 300ms 防抖
- 长列表虚拟滚动
- 所有异步操作有 loading 和 error 状态
- 表单提交防重复点击
- 页面切换未保存给确认

请按以下流程推进，每阶段让我确认后再继续：

阶段 0：用 /understand --language zh --full 建立项目知识图谱
阶段 1：/comet-open 需求探索与澄清
阶段 2：/comet-design 深度设计（含类型契约）
阶段 3：/comet-build 后端先出 Swagger 骨架 → 前后端并行开发
阶段 4：/comet-verify 验证
阶段 5：/comet-archive 归档
```

---

## 附录 B：各阶段快速提示词

### 快速需求澄清

```
PRD：docs/prd/xxx.md，接口文档：docs/api-external/xxx.md
请用 Comet Open 做需求澄清，输出：完整性分析 + 接口对齐 + 风险清单 + 问题清单
```

### 快速设计

```
本期范围已确认，技术栈 Vue 3 + Spring Boot。
请用 Comet Design 产出：数据库设计 + API 设计 + 前后端类型契约 + 前后端架构
```

### 快速开发

```
设计已确认，类型契约已写入代码。
请用 Comet Build：后端先出 Controller 骨架（Swagger）→ 前后端并行开发
```

### Bug 修复

```
发现 Bug：[描述]
请用 systematic-debugging 排查，先分析原因再修复
```

### 代码审查

```
请审查 [模块]，关注：设计一致性 / 性能 / 异常处理 / 安全性 / 可维护性
```

---

## 附录 C：常用命令速查

| 命令 | 用途 | 时机 |
|------|------|------|
| `/understand --language zh --full` | 建立/重建知识图谱 | 项目初始化 |
| `/understand --auto-update` | 增量更新知识图谱 | 每次大改后 |
| `/understand-dashboard` | 可视化架构 | 需要理解项目结构时 |
| `/understand-explain [文件]` | 深入解释文件 | 看别人代码时 |
| `/understand-diff` | 分析变更影响 | 看 PR / diff 时 |
| `/comet` | 自动检测阶段 | 不确定当前该干嘛时 |
| `/comet-open` | 需求探索 | 接到新需求 |
| `/comet-design` | 深度设计 | 需求确认后 |
| `/comet-build` | 计划与构建 | 设计确认后 |
| `/comet-verify` | 验证收尾 | 开发完成后 |
| `/comet-archive` | 归档 | 验证通过后 |
| `/comet-hotfix` | Bug 修复快捷路径 | 修 Bug |
| `/comet-tweak` | 小改动快捷路径 | 改文案/配置/样式 |

---

## 附录 D：关键决策速查

| 问题 | 建议 |
|------|------|
| 代码拉下来第一件事？ | `/understand --language zh --full` |
| PRD 来了第一件事？ | `/comet-open` 做需求澄清 |
| 前后端谁先写？ | 后端先出 Swagger 骨架，前端基于文档并行开发 |
| Mock 怎么做？ | 后端 Swagger 出来前用 vite-plugin-mock，出来后切真实接口 |
| 性能约束谁定？ | **你**在设计阶段通过提示词告诉 AI，AI 不会主动加 |
| 缓存策略谁定？ | **你**指定哪些数据要缓存，AI 按你的要求实现 |
| 组件库选什么？ | 你在提示词里指定 |
| 需要写测试吗？ | 关键业务逻辑用 TDD，简单 CRUD 可后补 |
| AI 代码能直接合入吗？ | 不能，必须你审查和测试后再合入 |

---

> **版本**：v3.0
> **核心变化**：将 Codegraph 作为阶段 0 前置步骤，贯穿整个工作流
