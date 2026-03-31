# nanobot 项目目录学习笔记

这个仓库是一个以 `nanobot/` 为核心的轻量级 AI 助手框架，整体可以分为源码、桥接、文档、测试和工程配置几层。

## 顶层目录作用

### `nanobot/`
主 Python 包，是项目核心源码目录。CLI、Agent 循环、模型 Provider、聊天渠道接入、配置、调度、技能系统等都在这里。

### `bridge/`
Node/TypeScript 写的桥接程序。当前主要用于 WhatsApp，对接 Baileys，再与 Python 主程序通信。

### `tests/`
测试目录，按模块拆分，覆盖 agent、channels、cli、config、cron、providers、security、tools 等功能。

### `docs/`
补充文档目录。目前主要包含 Python SDK 与渠道插件开发说明。

### `case/`
演示素材目录，保存 README 中引用的 GIF，用于展示搜索、记忆、排程、写代码等场景。

### `.github/`
GitHub 配置目录。目前主要包含 CI 工作流。

### `.venv/`
本地 Python 虚拟环境目录，不属于项目源码。

### `.git/`
Git 仓库元数据目录。

## `nanobot/` 主包内部结构

### `nanobot/agent/`
Agent 核心执行层。

- `loop.py`：Agent 主循环
- `runner.py`：运行与调度执行
- `hook.py`：生命周期钩子
- `context.py`：上下文组织
- `memory.py`：记忆管理
- `subagent.py`：子代理能力
- `skills.py`：技能装载与处理
- `tools/`：Agent 可调用工具实现

### `nanobot/agent/tools/`
工具系统实现目录，包括：

- `shell.py`：Shell/命令执行
- `filesystem.py`：文件系统访问
- `web.py`：网页搜索与抓取
- `message.py`：消息相关工具
- `cron.py`：定时任务工具
- `mcp.py`：MCP 工具集成
- `spawn.py`：派生任务或子执行单元
- `registry.py` / `base.py`：工具注册与抽象基类

### `nanobot/api/`
OpenAI 兼容 HTTP API 层。`server.py` 暴露 `/v1/chat/completions`、`/v1/models`、`/health` 等接口。

### `nanobot/bus/`
内部消息总线和事件队列，用于模块间事件传递。

### `nanobot/channels/`
聊天平台接入层，适配不同外部消息渠道。当前已支持：

- Feishu
- Slack
- Telegram
- Discord
- Email
- QQ
- Matrix
- WhatsApp
- Weixin / WeCom
- DingTalk
- 以及部分兼容渠道实现

### `nanobot/cli/`
命令行交互层。`commands.py` 是 CLI 主入口，负责终端交互、命令处理、输出渲染等。

### `nanobot/command/`
内建命令及命令路由层，用于把类似 `/status`、`/restart` 的命令分发到实现逻辑。

### `nanobot/config/`
配置系统，包括配置 schema、配置加载和路径管理。

### `nanobot/cron/`
定时任务能力，包括任务类型定义和调度服务。

### `nanobot/heartbeat/`
心跳与保活服务，负责 Agent 运行过程中的状态维护。

### `nanobot/providers/`
大模型 Provider 适配层，封装不同模型后端的统一调用方式。目前可见的实现包括：

- OpenAI 兼容 Provider
- Anthropic Provider
- Azure OpenAI Provider
- OpenAI Codex Provider
- 转写相关 Provider 辅助模块

### `nanobot/security/`
安全相关逻辑。目前可见的是网络访问控制模块。

### `nanobot/session/`
会话管理层，负责对话历史与会话隔离。

### `nanobot/skills/`
内置技能目录。每个技能通常以独立目录存在，包含 `SKILL.md`，有些还带脚本。当前可见技能包括：

- `clawhub`
- `cron`
- `github`
- `memory`
- `skill-creator`
- `summarize`
- `tmux`
- `weather`

### `nanobot/templates/`
模板和提示词目录，包含：

- `AGENTS.md`
- `TOOLS.md`
- `SOUL.md`
- `USER.md`
- `HEARTBEAT.md`
- `memory/MEMORY.md`

这些文件用于初始化工作区、构建系统提示和记忆模板。

### `nanobot/utils/`
通用辅助工具与评估辅助逻辑。

## `tests/` 结构

### `tests/agent/`
测试 Agent 主循环、记忆、runner、hook、session 历史、技能脚本等。

### `tests/channels/`
测试各聊天平台适配实现。

### `tests/cli/`
测试命令行行为与交互逻辑。

### `tests/config/`
测试配置路径解析和配置迁移。

### `tests/cron/`
测试定时任务服务和相关工具。

### `tests/providers/`
测试不同模型 Provider 的适配与重试逻辑。

### `tests/security/`
测试安全限制，如网络访问控制。

### `tests/tools/`
测试工具系统，如 web、filesystem、message、mcp、exec 安全等。

## 顶层重要文件

### `README.md`
项目主说明文档，包含功能、安装、架构、快速开始、项目结构等说明。

### `pyproject.toml`
Python 包配置文件，定义项目依赖、构建方式、CLI 入口、测试和 Ruff 配置。

### `Dockerfile` / `docker-compose.yml`
容器化部署相关配置。

### `core_agent_lines.sh`
统计核心 Agent 代码行数的脚本。

### `COMMUNICATION.md`
社区与交流群说明。

### `CONTRIBUTING.md`
贡献指南。

### `SECURITY.md`
安全说明与漏洞处理规范。

### `uv.lock`
依赖锁文件。

## 当前理解的入口关系

- `nanobot/__main__.py`：支持 `python -m nanobot`
- `nanobot/cli/commands.py`：CLI 主入口
- `nanobot/nanobot.py`：Python SDK 的高层封装入口
- `nanobot/api/server.py`：HTTP API 服务入口

## 后续可继续分析的方向

1. CLI 启动后如何初始化配置、Provider 和 AgentLoop
2. AgentLoop 如何组织上下文、工具调用和消息回写
3. Channels 如何接入统一消息总线
4. Skills 与 Templates 如何参与工作区初始化和系统提示构造
