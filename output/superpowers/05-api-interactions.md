# 接口与集成图

## 外部接口总览

```mermaid
flowchart TB
    subgraph Superpowers["Superpowers 系统"]
        A1[Skill Interface
           Markdown Format]
        A2[Command Interface
           Markdown Format]
        A3[Agent Interface
           Markdown Format]
        A4[Hook Interface
           Script Format]
    end
    
    subgraph HostPlatforms["宿主平台"]
        B1[Claude Code
           Plugin API]
        B2[Cursor
           Plugin API]
        B3[Codex
           Config-based]
        B4[OpenCode
           Config-based]
    end
    
    subgraph AIAPIs["AI 服务 APIs"]
        C1[Anthropic Messages API
           Claude Models]
        C2[OpenAI Chat API
           GPT-4/Codex]
        C3[MCP Protocol
           JSON-RPC 2.0]
    end
    
    subgraph Tools["工具集成"]
        D1[Git CLI
           Worktree/Branch]
        D2[File System
           Read/Write]
        D3[Test Runners
           Jest/Pytest/Go test]
        D4[Linters
           ESLint/Ruff]
    end
    
    Superpowers --> HostPlatforms
    HostPlatforms --> AIAPIs
    HostPlatforms --> Tools
    AIAPIs --> C3
    
    style A1 fill:#c8e6c9
    style A2 fill:#c8e6c9
    style A3 fill:#c8e6c9
    style C1 fill:#e3f2fd
    style C2 fill:#e3f2fd
    style C3 fill:#fff3e0
```

> **图注**：Superpowers 通过宿主平台与外部服务集成。技能/命令/代理以 Markdown 格式定义，宿主平台加载并执行。AI 服务提供模型能力，工具集成提供开发环境操作。

---

## Anthropic Claude API 集成

### Messages API 调用

```mermaid
sequenceDiagram
    participant S as Superpowers
    participant H as Host (Claude Code)
    participant A as Anthropic API
    
    S->>H: 1. 构建请求
    Note over S: 包含：
    Note over S: - system prompt
    Note over S: - messages history
    Note over S: - tools definition
    Note over S: - skill instructions
    
    H->>A: 2. POST /v1/messages
    Note over H,A: Headers:
    Note over H,A: Authorization: Bearer {api_key}
    Note over H,A: Content-Type: application/json
    Note over H,A: anthropic-version: 2023-06-01
    
    A->>A: 3. 处理请求
    
    alt 流式响应
        A-->>H: 4. SSE: content_block_delta
        H-->>S: 5. 流式回调
        S-->>S: 6. 更新 UI/状态
        
        A-->>H: 7. SSE: content_block_stop
    end
    
    alt 工具调用
        A-->>H: 8. SSE: tool_use
        H->>S: 9. 工具调用事件
        
        S->>S: 10. 执行工具
        Note over S: Read/Write/Bash/Skill
        
        S->>H: 11. 工具结果
        H->>A: 12. POST tool_result
        A->>A: 13. 继续生成
        A-->>H: 14. 后续响应
    end
    
    A-->>H: 15. message_stop
    H-->>S: 16. 响应完成
```

### 请求体格式

```json
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 8192,
  "system": "You are a software engineering assistant...",
  "messages": [
    {
      "role": "user",
      "content": "Implement user authentication"
    },
    {
      "role": "assistant",
      "content": "I'll help you implement user authentication..."
    }
  ],
  "tools": [
    {
      "name": "Read",
      "description": "Read a file from the filesystem",
      "input_schema": {
        "type": "object",
        "properties": {
          "file_path": {"type": "string"}
        },
        "required": ["file_path"]
      }
    },
    {
      "name": "Skill",
      "description": "Invoke a Superpowers skill",
      "input_schema": {
        "type": "object",
        "properties": {
          "skill_name": {"type": "string"},
          "context": {"type": "object"}
        },
        "required": ["skill_name"]
      }
    }
  ],
  "stream": true
}
```

### 工具定义

| 工具 | 用途 | 输入 | 输出 |
|------|------|------|------|
| **Read** | 读取文件 | `file_path: string` | 文件内容 |
| **Write** | 写入文件 | `file_path: string, content: string` | 成功/失败 |
| **Bash** | 执行命令 | `command: string, timeout?: number` | stdout/stderr |
| **Skill** | 调用技能 | `skill_name: string, context: object` | 技能执行结果 |
| **Subagent** | 派遣子代理 | `agent_type: string, task: string, context: object` | 子代理输出 |

---

## MCP (Model Context Protocol) 集成

### MCP 架构

```mermaid
flowchart TB
    subgraph MCPHost["MCP Host (Superpowers)"]
        A1[MCP Client Manager]
        A2[Tool Registry]
        A3[Resource Manager]
    end
    
    subgraph Transport["传输层"]
        B1[stdio
           本地进程]
        B2[SSE
           HTTP Streaming]
        B3[HTTP
           REST API]
    end
    
    subgraph MCPServers["MCP Servers"]
        C1[GitHub Server
           Issues/PRs]
        C2[PostgreSQL Server
           Database]
        C3[Filesystem Server
           File Access]
        C4[Custom Server
           Business Logic]
    end
    
    A1 -->|Initialize| B1
    A1 -->|Connect| B2
    A1 -->|Request| B3
    
    B1 -->|Spawn| C1
    B2 -->|Stream| C2
    B3 -->|REST| C3
    B3 -->|REST| C4
    
    C1 -->|Tools| A2
    C2 -->|Tools| A2
    C3 -->|Resources| A3
```

### MCP 协议流程

```mermaid
sequenceDiagram
    participant H as MCP Host
    participant T as Transport
    participant S as MCP Server
    
    H->>T: 1. 建立连接
    T->>S: 2. 连接就绪
    
    H->>S: 3. initialize 请求
    Note over H,S: {
    Note over H,S:   "protocolVersion": "2024-11-05",
    Note over H,S:   "capabilities": {...},
    Note over H,S:   "clientInfo": {...}
    Note over H,S: }
    
    S-->>H: 4. initialize 响应
    Note over S,H: {
    Note over S,H:   "protocolVersion": "2024-11-05",
    Note over S,H:   "capabilities": {...},
    Note over S,H:   "serverInfo": {...}
    Note over S,H: }
    
    H->>S: 5. tools/list 请求
    S-->>H: 6. 可用工具列表
    
    Note over H: 工具可用，可调用
    
    H->>S: 7. tools/call
    Note over H,S: {
    Note over H,S:   "name": "query",
    Note over H,S:   "arguments": {"sql": "SELECT * FROM users"}
    Note over H,S: }
    
    S->>S: 8. 执行工具
    
    S-->>H: 9. 工具结果
    Note over S,H: {
    Note over S,H:   "content": [...],
    Note over S,H:   "isError": false
    Note over S,H: }
```

### MCP 配置示例

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxx"
      },
      "transport": "stdio"
    },
    "postgres": {
      "url": "http://localhost:3001/sse",
      "transport": "sse"
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/projects"],
      "transport": "stdio"
    }
  }
}
```

---

## Git 集成

### Git Worktree 操作

```mermaid
flowchart LR
    A[主分支 main] --> B[创建 worktree]
    B --> C[新分支 feature-x]
    
    C --> D[独立工作目录]
    C --> E[独立 node_modules]
    C --> F[独立 .git]
    
    D --> G[子代理开发]
    E --> G
    
    G --> H[提交代码]
    H --> I[测试通过]
    I --> J[合并到 main]
    
    J --> K[清理 worktree]
```

### Git 命令集成

| 操作 | 命令 | 用途 |
|------|------|------|
| **创建 Worktree** | `git worktree add -b feature-x ../feature-x` | 隔离开发环境 |
| **切换分支** | `git checkout -b feature-x` | 在新分支工作 |
| **提交代码** | `git add . && git commit -m "msg"` | 保存进度 |
| **查看状态** | `git status` | 检查变更 |
| **合并分支** | `git merge feature-x` | 完成功能 |
| **清理 Worktree** | `git worktree remove feature-x` | 清理环境 |

### 子代理 Git 工作流

```mermaid
sequenceDiagram
    participant M as 主控制器
    participant S as 实现者子代理
    participant G as Git 仓库
    
    M->>G: 1. 创建 worktree
    G-->>M: 2. 返回 worktree 路径
    
    M->>S: 3. 派遣子代理
    Note over M,S: 提供 worktree 路径作为工作目录
    
    S->>G: 4. 编写代码
    S->>G: 5. 编写测试
    S->>G: 6. 运行测试
    G-->>S: 7. 测试结果
    
    S->>G: 8. git add .
    S->>G: 9. git commit -m "Implement feature"
    G-->>S: 10. 提交成功
    
    S-->>M: 11. 任务完成，返回 commit SHA
    
    M->>G: 12. 在 main worktree 审查代码
    
    alt 通过审查
        M->>G: 13. git merge feature-x
        M->>G: 14. 删除 worktree
    else 需要修改
        M->>S: 15. 派遣修复子代理
        S->>G: 16. 修改代码
        S->>G: 17. git commit --amend
    end
```

---

## 平台集成详情

### Claude Code 集成

```mermaid
flowchart TB
    subgraph Plugin["Claude Code Plugin"]
        A1[Plugin Manifest
           manifest.json]
        A2[Plugin Entry
           index.js]
        A3[Skill Loader
           loadSkills()]
    end
    
    subgraph Marketplace["Marketplace"]
        B1[obra/superpowers-marketplace]
        B2[Plugin Registry]
        B3[Auto Update]
    end
    
    subgraph Commands["新增命令"]
        C1[/plugin install]
        C2[/plugin update]
        C3[/skill]
        C4[/brainstorm]
        C5[/write-plan]
    end
    
    A1 --> A2
    A2 --> A3
    
    B1 -->|Register| B2
    B2 -->|Install| A2
    B3 -->|Update| A2
    
    A2 -->|Add| C1
    A2 -->|Add| C2
    A2 -->|Add| C3
    A2 -->|Add| C4
    A2 -->|Add| C5
```

**安装命令**：
```bash
# 注册市场
/plugin marketplace add obra/superpowers-marketplace

# 安装插件
/plugin install superpowers@superpowers-marketplace

# 更新插件
/plugin update superpowers
```

### Cursor 集成

```mermaid
flowchart TB
    subgraph CursorPlugin["Cursor Plugin"]
        A1[Agent Chat Integration]
        A2[Skill Auto-Trigger]
        A3[Command Palette]
    end
    
    subgraph Installation["安装"]
        B1[Cursor Settings]
        B2[Plugin Marketplace]
        B3[Install superpowers]
    end
    
    A1 -->|Trigger| A2
    A2 -->|Skills| A1
    A1 -->|Commands| A3
    
    B1 --> B2
    B2 --> B3
    B3 --> A1
```

**安装命令**：
```
/plugin-add superpowers
```

### Codex / OpenCode 集成

```mermaid
flowchart LR
    A[Codex/OpenCode] -->|Fetch| B[INSTALL.md]
    B --> C[安装脚本]
    C --> D[下载 Superpowers]
    D --> E[配置环境]
    E --> F[加载 Skills]
    F --> G[开始使用]
```

**安装指令**：
```
Fetch and follow instructions from 
https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.codex/INSTALL.md
```

---

## 自定义扩展接口

### 创建新 Skill

```yaml
# skills/my-skill/SKILL.md
---
name: my-skill
description: "Use when [trigger condition]"
type: flexible
autoTrigger: true
---

# My Skill

## Overview
技能概述

## When to Use
使用场景

## Checklist
1. **Step 1** - Do something
2. **Step 2** - Do another thing
3. **Step 3** - Complete

## Examples
示例代码
```

### 创建新 Command

```markdown
# commands/my-command.md
---
name: my-command
trigger: "/mycommand"
description: "Does something useful"
---

# My Command

## Usage
/mycommand [argument]

## What it does
描述功能

## Prompt Template
执行命令时使用的提示词
```

### 创建新 Agent

```markdown
# agents/my-agent.md
---
name: MyAgent
description: "Specialized agent for X"
model: claude-3-haiku-20240307
allowedTools:
  - Read
  - Write
  - Bash
---

# My Agent

你是一个专门做 X 的代理。

## Responsibilities
- 责任1
- 责任2

## Guidelines
指导原则
```

---

## 错误处理与恢复

```mermaid
flowchart TD
    A[API 调用] --> B{成功?}
    
    B -->|是| C[正常处理]
    B -->|否| D{错误类型?}
    
    D -->|Rate Limit| E[指数退避重试]
    D -->|Auth Error| F[提示检查 API Key]
    D -->|Timeout| G[重试最多3次]
    D -->|Bad Request| H[检查请求参数]
    D -->|Server Error| I[提示稍后重试]
    
    E --> J{成功?}
    G --> J
    
    J -->|是| C
    J -->|否| K[记录错误]
    
    F --> L[打开设置]
    H --> M[修复参数]
    I --> N[等待后重试]
    
    K --> O[返回错误信息]
    L --> P[用户修复]
    M --> A
    N --> A
    
    C --> Q[返回结果]
    
    style C fill:#c8e6c9
    style Q fill:#c8e6c9
```

> **图注**：错误处理采用分级策略。网络错误自动重试，认证错误引导用户修复配置，参数错误提供调试信息。所有错误都记录日志，方便排查问题。
