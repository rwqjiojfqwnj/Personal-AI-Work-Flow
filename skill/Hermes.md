近期，OpenClaw Agent之风未散， Agent圈又引来新的Agent—Hermes  Agent。**OpenClaw 和 Hermes 都属于通用 Agent 系统，它们都不是单点脚本，也不是某个聊天渠道里的 bot。它们都在尝试把模型、工具、会话、记忆、Skills、消息入口和本地运行环境接成一套可以长期使用的系统。注意长期使用,以往AI,大家基本用完就结束,针对复杂任务非常有限**。\*\*\*\*

**01 Hermes Agent 是什么？**

Hermes Agent 是由 Nous Research 在 2026 年 2 月开源发布的自主 AI 智能体。它的定位非常明确——不是绑在 IDE 里的编码副驾驶，也不是套在某个 API 上的聊天机器人，而是一个运行在你自己服务器上、能持续记忆、越用越强的自主智能体 \*\*\*\*。\*\*\*\*Hermes Agent 是开源的, 主要有以下特征:

内置了一个学习循环：在完成复杂任务之后，它能将解题思路保存为可复用的"技能（Skill）"，以备下次使用。它跨 Session 维持持久记忆，并逐步积累对你这个用户的认知模型。你可以接入任意 LLM 提供商——OpenAI、Anthropic、OpenRouter（200+ 模型）或自托管端点。

自我学习闭环:与大多数 Agent 框架不同，Hermes 以 AIAgent 执行循环本身作为核心同步编排引擎，围绕它构建了 Gateway、Cron 调度器、工具运行时、ACP（Agent Communication Protocol，一种让代码编辑器等外部工具与 Agent 对话的标准协议）、基于 SQLite 的 Session 持久化，以及 RL 强化学习环境。

它的学习闭环可以概括为三个层次：

1.  **持久记忆（Memory）** Agent 维护两个简洁的核心文件：`MEMORY.md`（存储环境事实、规范和经验教训）和 `USER.md`（存储你的偏好和沟通风格），在每次 Session 开始时注入到系统提示。同时，它还对所有历史 Session 进行全文检索（FTS5 + SQLite），可以回忆数周前的对话。

2.  **技能系统（Skills）** 当一个复杂任务（通常超过 5 次工具调用）完成后，Agent 能自主生成一份技能文档——一个包含操作步骤、注意事项和验证方法的结构化 Markdown 文件。下次遇到类似任务时，它会直接加载这份技能，而不是从头摸索。技能还会在使用中自我改进。这些技能文件遵循 agentskills.io 开放标准，可以跨平台共享。

3.  **用户建模（User Modeling）** 在多次 Session 中，Hermes 会逐步建立对你这个用户的表征——你的工作方式、偏好，以及你已经做过的决策，最终形成一个越来越个性化的助手。

**在完成任务后，系统会评估任务执行过程是否值得记录成可复用的技能。如果值得，它就会生成“技能文档”，供后续任务调用。这使得 Hermes 在面对相似任务时，能快速高效地完成工作。其核心流程：** **输入 → 推理 → 工具调用 → 执行 → 写入记忆 → 输出**

## 主要功能:

*   **40+ 内置工具**：包括网页搜索、浏览器自动化和视觉处理。

*   **多平台接入**：Telegram、Discord、Slack、WhatsApp、Signal 和命令行——通过单一 Gateway 进程统一管理。

*   **6 种运行后端**：本地、Docker、SSH、Daytona、Singularity 和 Modal；Daytona 和 Modal 支持 Serverless 休眠，不用时几乎零成本。

*   **自然语言** **Cron** **调度**：你只需说"每个工作日早上 9 点汇总我的收件箱发到 Slack"，Agent 就会自动建立计划任务，无需配置文件。

*   **MCP** **支持**：可以通过配置文件接入任意 Model Context Protocol 服务器。

*   **研究就绪**：内置批量轨迹生成、Atropos RL 环境和轨迹压缩，可用于训练下一代工具调用模型。

    **02 与 OpenClaw 的对比**

目前 AI Agent 领域最常拿来与 Hermes 比较的开源项目是 

**OpenClaw**，两者代表了截然不同的设计哲学。一句话概括:OpenClaw 专注于集成的广度，而 Hermes Agent 专注于学习的深度。

<table interlaced="enabled" style="border-collapse: collapse;"><tbody><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">维度</span></span></section></td><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">OpenClaw</span></span></section></td><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Hermes Agent</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">核心设计</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Gateway 控制面（中央控制器协调一切）</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Agent 执行循环（do-learn-improve）</span></span></section></td></tr><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">记忆方式</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">纯文件存储，人类可读可编辑</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">四层体系：Session 历史 + 用户建模 + FTS5 检索 + LLM 摘要</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">技能来源</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">主要靠人工编写，社区生态丰富（44,000+）</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Agent 自动从任务中提取并持续优化</span></span></section></td></tr><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">平台覆盖</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">50+ 消息平台，社区生态覆盖最广</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">6 个主流平台，支持 Signal</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">安全控制</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">严格的审批系统，可逐条控制命令权限</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">默认更安全，容器隔离，支持命名空间</span></span></section></td></tr><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">适用场景</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">需要广泛接入、快速部署、精细权限控制</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">重复性任务、长期个性化、深度学习积累</span></span></section></td></tr></tbody></table>

![图片|433](attachment/001.webp)

把 OpenClaw 想象成一个严格按说明书办事的高能助手；把 Hermes 想象成一个任务完成后会自己写说明书、下次做得更好的助手。值得一提的是，如果你之前使用的是 OpenClaw，Hermes 提供了自动迁移工具，可以导入你在 OpenClaw 中的设置、记忆、技能和 API 密钥。

**03 与其他 Agent 框架的对比**

<table interlaced="enabled" style="border-collapse:collapse;min-width:242px;"><tbody><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">框架</span></span></section></td><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">定位</span></span></section></td><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">与 Hermes 的核心差异</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">AutoGPT</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">任务分解 + 自主执行</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">专注任务执行，无持久学习机制</span></span></section></td></tr><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">LangChain</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">开发者工具链</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">构建 AI 应用的框架层，非端到端 Agent</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Claude Code</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">IDE 内编码助手</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">会话结束即失忆，无跨 Session 技能积累</span></span></section></td></tr><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">OpenClaw</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">生态广度优先</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">人工维护技能，不具备自动学习能力</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Hermes Agent</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">学习深度优先</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">自动生成技能、构建用户模型、越用越强</span></span></section></td></tr></tbody></table>

问题来了,那么谁适合用 Hermes？Hermes Agent 在你需要与 AI 进行长期协作时最能发挥优势——比如用几周或几个月积累领域知识、培养复杂偏好，或者希望 AI 主动提升自身能力。简单来说：

*   **适合**：重复性工作流、长期项目、希望 AI 越用越懂你的场景

*   **不适合**：一次性查询、需要严格权限审批、想要即开即用的大型生态

**04 安装教程**

在安装之前，确认你的环境满足以下条件：

*   操作系统：Linux / macOS / WSL2 / Android（Termux）

*   模型上下文：需要至少 64,000 tokens 上下文窗口的模型，较小的模型会在启动时被拒绝, 刚才说了它适合处理长期复杂任务,懂得都懂

*   Windows 用户：原生 Windows 不受支持，请先安装 WSL2，然后在 WSL2 终端内运行

    方式一：一键安装

适用于 Linux / macOS / WSL2 / Android（Termux）：

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

这条命令会自动创建虚拟环境，并安装所有 Python 和 Node.js 依赖。安装完成后，向导会引导你完成 LLM 提供商的配置。

方式二：手动安装

```bash
# 1. 安装 uv（Python 包管理器）
curl -LsSf https://astral.sh/uv/install.sh | sh
# 2. 克隆仓库（含子模块）
git clone --recurse-submodules https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
# 3. 创建 Python 3.11 虚拟环境
uv venv venv --python 3.11
export VIRTUAL_ENV="$(pwd)/venv"
# 4. 安装所有依赖
uv pip install -e ".[all]"
npm install   # 可选，用于浏览器工具和 WhatsApp
# 5. 初始化配置目录
mkdir -p ~/.hermes/{cron,sessions,logs,memories,skills,pairing,hooks,image_cache,audio_cache}
cp cli-config.yaml.example ~/.hermes/config.yaml
touch ~/.hermes/.env
echo 'OPENROUTER_API_KEY=sk-or-v1-你的key' >> ~/.hermes/.env
# 6. 创建全局命令
mkdir -p ~/.local/bin
ln -sf "$(pwd)/venv/bin/hermes" ~/.local/bin/hermes
```

### 不论哪种安装方式, 和openclaw一样,安装较简单, 难在配置, 具体如何配置 LLM 模型, 安装完成后，运行以下命令配置你的模型提供商：

```bash
hermes model   # 选择提供商和模型
```

Hermes 支持多种推理提供商，选定后可随时切换，无需修改任何代码。

<table interlaced="enabled" style="border-collapse: collapse;"><tbody><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">提供商</span></span></section></td><td style="color:rgb(0, 0, 0);font-weight:bold;text-align:center;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">说明</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">OpenRouter</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">接入 200+ 模型，灵活性最高</span></span></section></td></tr><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Nous Portal</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">官方推荐，直接支持 Hermes 模型家族</span></span></section></td></tr><tr class="ue-table-interlace-color-double"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">OpenAI</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">稳定可靠，适合生产环境</span></span></section></td></tr><tr class="ue-table-interlace-color-single"><td style="color:rgb(0, 0, 0);font-weight:bold;word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">本地 Ollama</span></span></section></td><td style="color:rgb(0, 0, 0);word-wrap:break-word;word-break:break-word;white-space:pre-wrap;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">完全离线，数据不出本地</span></span></section></td></tr></tbody></table>

验证安装

```bash
hermes version   # 检查版本
hermes doctor    # 运行诊断，确认配置正确
hermes           # 启动 Agent，开始对话

配置消息平台（可选）
```

消息 Gateway 让你能通过 Telegram、Discord、Slack、WhatsApp、Signal 与 Agent 交互，所有平台通过一个后台进程统一管理。

```nginx
hermes gateway setup   # 交互式配置平台
```

以 Telegram 为例：

```bash
在 BotFather 创建 Bot，获取 Token
运行 
hermes gateway setup
，选择 Telegram，粘贴 Token
限制访问权限（推荐）：
# 找到你的 Telegram user ID（发消息给 @userinfobot）
export TELEGRAM_ALLOWED_USERS=你的user_id
```

配置完成后，安装为系统服务，开机自启、崩溃自动重启：

```bash
hermes gateway install          # macOS 或 Linux 用户级服务
sudo hermes gateway install --system  # Linux 系统级服务
```

***

### 设置定时任务（可选）

只需用自然语言描述你的需求，Agent 就会自动创建 Cron 任务：

```shell
> 每个工作日早上 9 点，抓取 Hacker News 的 AI 相关内容，整理成摘要发到 Telegram
```

Agent 会自动配置好调度，不需要手写任何 crontab。

***

### 安全建议

持久化运行的 Agent 拥有 Shell 访问权限，建议注意以下几点：

*   **切换到 Docker 后端**：将 Agent 的终端访问隔离在容器内，限制它能操作的宿主机文件范围

```bash
hermes config set terminal.backend docker

配置访问白名单
```

*   ：在 `~/.hermes/config.yaml` 的 `gateway` 段设置允许发送命令的用户

*   **定期更新**：

```bash
hermes update   # 拉取最新安全修复，不影响你的配置和记忆文件
```

***

### 从 OpenClaw 迁移

如果你目前在使用 OpenClaw，为了得到用户, Hermes 提供了自动迁移工具，可以导入你的设置、记忆、技能和 API 密钥：

```bash
hermes claw migrate            # 交互式迁移（完整迁移）
hermes claw migrate --dry-run  # 预览迁移内容，不实际执行
hermes claw migrate --preset user-data  # 仅迁移数据，不迁移密钥



05 架构分析

5.1 Hermes 的定位：不是聊天壳，而是 Agent 运行时
```

Hermes Agent 的官方介绍非常明确：它是一个 self-improving AI agent，会从经验中创建 skills，在使用中不断改进，并搜索自己的历史对话以回忆上下文。它支持运行在低成本 VPS、GPU 集群或 serverless 环境中，而且并不绑定在用户本机。这个定位决定了它的设计方向：不是围绕“单轮问答”做功能堆叠，而是围绕“长期驻留、持续成长、跨入口工作”构建整套系统。

从仓库与文档的结构看，Hermes 的重点也不是单一模型调用，而是一个可编排的执行框架。它把模型、工具、记忆、技能、配置、网关、终端后端和上下文治理拆成独立模块，再通过统一的 Agent 循环串起来。官方架构页甚至直接给出了依赖方向：

```bash
tools/registry.py
 → 
tools/*.py
 → 
model_tools.py
 → 
run_agent.py / cli.py / batch_runner.py / environments/
。
```

### 5.2 整体架构：一个统一内核，多个入口

Hermes 的第一层设计原则是“入口多样，但内核统一”。CLI、消息网关、批处理、环境执行和 IDE 集成都不是独立实现的 Agent，而是把输入转换为统一消息结构后，交给 `AIAgent` 处理。官方架构文档明确说，所有 agent sessions 都经过这个类。这类设计最大的好处，是能力可以跨入口复用。你在 CLI 里调用过的工具、积累过的记忆、创建过的 skills，不需要为 Telegram、Slack 或其他平台重写一遍。对于一个强调长期驻留与持续改进的 Agent 系统来说，这种统一内核非常关键。

### 5.3 架构图

![图片|697](attachment/002.webp)

### 5.4 核心执行链路：从输入到输出，始终是一条闭环

Hermes 的主循环可以理解为：**接收输入 → 组装上下文 → 调用模型 → 解析工具调用 → 执行工具 → 回填结果 → 继续迭代 → 生成最终输出**。官方架构页说明，这个循环同步运行；若工具本身是异步的，则通过内部桥接方式执行。

这意味着 Hermes 真正的控制点，不在“模型回答了什么”，而在“工具调用如何被安全、稳定、可持续地嵌入对话链路”。模型负责决策，框架负责执行，工具负责落地。这样一来，Agent 能力就从“文本生成”升级成“任务执行”。

### 5.5 工具层：registry 统一注册，避免散装调度

Hermes 的工具系统不是散点式挂接，而是通过 registry 做集中管理。官方文档说明，`tools/registry.py` 处于最底层，所有工具文件在导入时注册自己，`model_tools.py` 再负责发现和调度工具。这种设计避免了循环依赖，也让新增工具更容易融入系统。

同时，Hermes 还区分了普通工具和状态工具。官方在“添加工具”文档中提到，有些工具需要访问 agent 级状态，因此会被 `run_agent.py` 直接拦截处理，例如 `TodoStore`、`MemoryStore`、`session_search` 和 `delegate_task`。这说明它不是把所有工具都当成无状态函数，而是对“与会话强绑定的能力”做了专门处理。

5.6 记忆层：把“事实”与“用户画像”分开存

Hermes 的持久记忆非常有代表性。官方说明它有两个核心记忆文件：`MEMORY.md` 和 `USER.md`。前者记录环境事实、项目约定、工具细节等广义事实；后者记录用户画像、偏好、工作风格与沟通习惯。两者都存放在 `~/.hermes/memories/` 下，并在会话开始时作为冻结快照注入系统提示词。

这种设计的价值在于，它把“系统事实”与“用户偏好”拆开了。事实层更偏向工作环境与项目知识，画像层更偏向人和协作方式。这样做既有利于检索，也有利于更新；更重要的是，它让 Agent 可以在不频繁重写系统上下文的情况下持续记忆，从而保持 prompt caching 的稳定性。

### 5.7 技能层：把“怎么做”沉淀为可复用的 procedural memory

Hermes 的 Skills 系统是它最有辨识度的设计之一。官方把 skills 定义为 markdown 文件，内容不是泛泛而谈的知识，而是针对某类任务的可复用步骤与方法；每个 skill 是一个目录，里面包含 `SKILL.md` 以及可选的辅助文件。

更重要的是，Skills 在调用时会被注入为 user message，而不是不断塞进 system prompt。官方明确表示，这样做是为了保留 Anthropic prompt caching。也就是说，Hermes 不是简单“记住一些 instructions”，而是把经验组织成一套能被按需加载、按需执行、还能自我改进的技能资产。

### 5.8 上下文治理：Prompt Caching + Context Compressor

Hermes 对上下文的组织非常讲究。官方配置文档说明，`~/.hermes/.env` 专门放 API keys、tokens 和密码，而 `~/.hermes/config.yaml` 是行为配置的权威来源；模型、终端、显示、记忆等设置都从这里读取。这样做的目的之一，就是减少会话过程中无意义的上下文变化。

当上下文真的变长时，Hermes 会启用 `ContextCompressor`。官方架构页说明，这个组件会对早期消息进行摘要压缩，只保留关键片段与最近上下文，从而避免长会话失控。对一个需要持续运行的 Agent 来说，这其实是一种“上下文熔断机制”：平时尽量稳定，临界时再折叠历史。

### 5.9 执行后端：让 Agent 不依赖本机

Hermes 的 shell 与文件执行并不绑定在用户电脑上。官方文档列出的 terminal backends 包括 local、Docker、SSH、Daytona、Singularity 和 Modal。它们分别对应本机执行、容器隔离、远程服务器、云沙箱和其他隔离运行环境。

这直接决定了 Hermes 的产品形态：它不是“打开一个聊天页面”，而是“把 Agent 放在某个长期运行的环境里，再通过不同入口去控制它”。这也是为什么官方一再强调它可以通过 Telegram 与运行在云 VM 上的 Agent 交互。

### 5.10 消息网关：把 Agent 变成随处可用的后台服务

Hermes 的 messaging gateway 不是一个简单转发层，而是一个持续运行的桥接服务。官方文档列出它支持 Telegram、Discord、Slack、WhatsApp、Signal 等平台，并说明 gateway 可以把这些平台的消息接入统一 Agent loop，再把结果回传给原平台。

从架构视角看，这意味着 Hermes 不只是“本地工具”，而是可以作为一个常驻后台服务来部署。对个人用户来说，它像一个随叫随到的远程助手；对团队场景来说，它更像一个跨平台的工作代理。

### 5.11 版本演进：Hermes 的方向越来越平台化

从 v0.3.0 的 release notes 可以看出，Hermes 已经把统一流式输出、插件架构、provider 重构、原生 Anthropic provider、智能审批、浏览器直连、ACP IDE 集成、Honcho memory、语音模式和持久 shell 放进了同一条演进路径里。

这说明它的演进方向并不是“多做几个 demo 功能”，而是在持续强化平台化、插件化、持久化和跨入口统一。换句话说，它正在从“一个能用的 Agent”走向“一个可持续增长的 Agent 运行时”。

### 5.12 优点与代价

Hermes 的优点很明显：统一内核、入口一致、工具注册集中、记忆分层、技能可复用、支持多终端后端、支持长会话压缩，而且能够把对话能力扩展到消息平台和 IDE 场景。对于一个开源 Agent 来说，这种系统完整度很高。

代价也同样明确：系统复杂度高，尤其是配置加载、缓存稳定性、上下文压缩、状态工具与普通工具的边界、跨平台会话隔离，以及安全审批逻辑，都会带来工程维护成本。官方在贡献指南里把 bug 修复、跨平台兼容、安全加固、性能与健壮性排在前列，这本身就说明它不是一个轻量级玩具项目。

### 5.13 结语：Hermes 最值得学习的，不是单点能力，而是系统化组织方式

Hermes Agent 最值得借鉴的地方，不是某一个工具，而是它的系统设计：把 **记忆、技能、工具、入口、执行后端、上下文治理** 全部放进一套统一的 Agent 运行时里，再通过持续积累，把“使用经验”变成“可复用能力”。它已经明显超出普通聊天机器人，更接近一个面向个人与团队的 Agent 基础设施。

一句话总结：**Hermes** **不是在回答问题，它是在构建一个会成长的工作系统。**

**推荐阅读:**

<https://mp.weixin.qq.com/s/dyamkjN7w-3q2ggzxRq0sg>

<https://mp.weixin.qq.com/s/MzB8-B00GVdJy22j42CRgg>

<https://mp.weixin.qq.com/s/oKuSgz5CP4aPOjt\\_o2Vi8g>

**更多合集文章请关注我的公众号，一起学习一起进步：**

[工具篇| Agent中的爱马仕—Hermes近期，OpenClaw Agent之风未散， Agent圈又引来新的Agen - 掘金](https://juejin.cn/post/7633369483193106486?searchId=202606281336143C43731922C952C6F4D9)