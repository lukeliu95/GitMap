# 数据关系图

## 实体关系图（ER图）

```mermaid
erDiagram
    CONVERSATION ||--o{ CHAT_MESSAGE : contains
    CONVERSATION ||--o{ TOOL_CALL : has
    CHAT_MESSAGE ||--o{ IMAGE_ATTACHMENT : has
    CHAT_MESSAGE ||--o{ CONTENT_BLOCK : contains
    
    CONVERSATION {
        string id PK "会话唯一标识"
        string title "会话标题"
        number createdAt "创建时间戳"
        number updatedAt "更新时间戳"
        string sessionId "SDK会话ID"
        string sdkSessionId "当前SDK会话ID"
        string forkSource "分支来源"
        string model "使用的模型"
        boolean planMode "是否规划模式"
    }
    
    CHAT_MESSAGE {
        string id PK "消息唯一标识"
        string role "角色: user/assistant"
        string content "消息内容"
        string displayContent "显示内容"
        number timestamp "时间戳"
        string currentNote "当前聚焦笔记"
        boolean isInterrupt "是否中断"
        boolean isRebuiltContext "是否重建上下文"
        number durationSeconds "响应耗时"
        string sdkUserUuid "SDK用户消息UUID"
        string sdkAssistantUuid "SDK助手消息UUID"
    }
    
    CONTENT_BLOCK {
        string type "类型: text/tool_use/thinking"
        string content "内容"
        string toolId "工具ID"
        number durationSeconds "思考时长"
    }
    
    IMAGE_ATTACHMENT {
        string id PK "附件ID"
        string name "文件名"
        string mediaType "媒体类型"
        string data "Base64数据"
        number size "文件大小"
        string source "来源: file/paste/drop"
    }
    
    TOOL_CALL {
        string id PK "调用ID"
        string toolName "工具名称"
        string params "参数"
        string result "结果"
        string status "状态"
    }
    
    SLASH_COMMAND {
        string id PK "命令ID"
        string name "命令名称"
        string description "描述"
        string content "提示词内容"
        string source "来源: builtin/user/plugin"
        array allowedTools "允许的工具"
    }
    
    CUSTOM_AGENT {
        string name PK "Agent名称"
        string prompt "系统提示词"
        array allowedTools "允许的工具"
        string model "指定模型"
    }
    
    MCP_SERVER {
        string name PK "服务器名称"
        string transport "传输方式"
        string url "连接地址"
        boolean enabled "是否启用"
    }
```

> **图注**：ER图展示了 Claudian 的核心数据实体。Conversation（会话）是顶层容器，包含多个 ChatMessage（消息）。消息可以包含图片附件和内容块。ToolCall 记录 AI 的工具调用历史。SlashCommand、CustomAgent、MCPServer 是配置实体，存储用户自定义的命令、代理和外部服务。

---

## 类图设计

```mermaid
classDiagram
    class ClaudianPlugin {
        +Settings settings
        +McpServerManager mcpManager
        +PluginManager pluginManager
        +AgentManager agentManager
        +StorageService storage
        +ClaudeCliResolver cliResolver
        +onload()
        +onunload()
        +loadSettings()
        +saveSettings()
    }
    
    class ClaudianView {
        +TabManager tabManager
        +TabBar tabBar
        +activateView()
        +createTab()
        +switchTab()
        +closeTab()
    }
    
    class ClaudianService {
        +SessionManager sessionManager
        +MessageChannel messageChannel
        +QueryOptionsBuilder queryBuilder
        +sendMessage()
        +createColdStartQuery()
        +handleStreamResponse()
    }
    
    class SessionManager {
        +Map sessions
        +createSession()
        +loadSession()
        +saveSession()
        +deleteSession()
    }
    
    class StorageService {
        +VaultFileAdapter vaultFiles
        +SessionStorage sessionStorage
        +SettingsStorage settingsStorage
        +SlashCommandStorage commandStorage
        +readFile()
        +writeFile()
        +loadData()
        +saveData()
    }
    
    class McpServerManager {
        +Map servers
        +loadServers()
        +testConnection()
        +getTools()
        +callTool()
    }
    
    class AgentManager {
        +Map agents
        +loadAgents()
        +getAgent()
        +invokeAgent()
    }
    
    class PluginManager {
        +Map plugins
        +loadPlugins()
        +enablePlugin()
        +disablePlugin()
    }
    
    class SecurityManager {
        +ApprovalManager approval
        +BlocklistChecker blocklist
        +BashPathValidator pathValidator
        +checkPermission()
        +validateCommand()
        +validatePath()
    }
    
    ClaudianPlugin --> ClaudianView
    ClaudianPlugin --> ClaudianService
    ClaudianPlugin --> StorageService
    ClaudianPlugin --> McpServerManager
    ClaudianPlugin --> AgentManager
    ClaudianPlugin --> PluginManager
    
    ClaudianView --> TabManager
    ClaudianService --> SessionManager
    ClaudianService --> MessageChannel
    
    StorageService --> VaultFileAdapter
    StorageService --> SessionStorage
    StorageService --> SettingsStorage
```

> **图注**：类图展示了 Claudian 的核心类结构。ClaudianPlugin 是主插件类，管理所有子系统。ClaudianView 负责 UI，ClaudianService 负责 AI 通信，StorageService 负责数据持久化。各种 Manager 类分别管理特定的功能模块（MCP、Agent、Plugin、Security）。

---

## 数据流向图

### 会话数据生命周期

```mermaid
flowchart TB
    subgraph 创建阶段
        A1[用户创建新会话] --> A2[生成 conversation ID]
        A2 --> A3[初始化 session 对象]
        A3 --> A4[保存到内存]
    end
    
    subgraph 使用阶段
        B1[用户发送消息] --> B2[添加 message 到 conversation]
        B2 --> B3[发送到 AI]
        B3 --> B4[接收流式响应]
        B4 --> B5[更新 message 内容]
        B5 --> B6[持久化到存储]
    end
    
    subgraph 工具调用
        C1[AI 请求工具] --> C2[创建 tool_call 记录]
        C2 --> C3[检查权限]
        C3 --> C4[执行工具]
        C4 --> C5[更新 tool_call 结果]
        C5 --> C6[发送结果给 AI]
    end
    
    subgraph 结束阶段
        D1[用户关闭会话] --> D2[保存完整会话]
        D2 --> D3[清理内存中的 session]
        D3 --> D4[保留持久化数据]
    end
    
    A4 --> B1
    B6 --> C1
    C6 --> B3
    B6 --> D1
```

> **图注**：会话数据经历创建、使用、结束三个阶段。使用阶段的消息是增量更新的，每次收到流式数据块都会更新消息内容并触发持久化。工具调用会在会话中创建独立的记录，方便后续审计和回放。

### 设置数据流

```mermaid
flowchart LR
    A[用户修改设置] --> B[ClaudianSettingTab]
    B --> C[更新 settings 对象]
    C --> D{设置类型?}
    
    D -->|Claudian 设置| E[保存到 data.json]
    D -->|CC 兼容设置| F[保存到 .claude/settings.json]
    D -->|Slash 命令| G[保存到 .claude/commands/]
    D -->|MCP 配置| H[保存到 .claude/mcp.json]
    
    E --> I[触发设置变更事件]
    F --> I
    G --> I
    H --> I
    
    I --> J[ClaudianPlugin 响应]
    J --> K[重新加载相关模块]
    
    K --> L[更新 UI 显示]
    K --> M[更新 AI 配置]
```

> **图注**：设置数据根据类型保存到不同位置，确保与 Claude Code CLI 的兼容性。Claudian 专有设置保存在 Obsidian 的 data.json 中，CC 兼容设置保存在 .claude/settings.json 中，实现跨工具配置共享。

---

## 数据存储详情

### 1. 会话存储（Obsidian 原生）

```typescript
// 存储位置: {vault}/.claude/sessions/{conversationId}.json
interface Conversation {
  id: string;           // "conv_xxxxxxxx"
  title: string;        // "React 学习讨论"
  createdAt: number;    // 时间戳
  updatedAt: number;
  sessionId: string | null;
  sdkSessionId: string | null;
  messages: ChatMessage[];
  model: string;
  planMode: boolean;
}

interface ChatMessage {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  timestamp: number;
  toolCalls?: ToolCallInfo[];
  images?: ImageAttachment[];
  contentBlocks?: ContentBlock[];
}
```

### 2. SDK 会话存储（Agent SDK 原生）

```typescript
// 存储位置: ~/.claude/projects/{projectName}/
// SDK 自动管理，包含完整的消息历史
// 用于 rewind、fork 等高级功能
```

### 3. 设置存储

```typescript
// Claudian 专有设置: {vault}/.obsidian/plugins/claudian/data.json
interface ClaudianSettings {
  userName: string;
  permissionMode: 'yolo' | 'safe' | 'plan';
  model: ClaudeModel;
  thinkingBudget: ThinkingBudget;
  enableBlocklist: boolean;
  blockedCommands: PlatformBlockedCommands;
  // ... 其他设置
}

// CC 兼容设置: {vault}/.claude/settings.json
interface CCSettings {
  $schema: string;
  permissions: {
    allow: string[];
    deny: string[];
    ask: string[];
  };
  model?: string;
  env?: Record<string, string>;
}
```

### 4. 斜杠命令存储

```typescript
// 用户命令: {vault}/.claude/commands/{commandName}.md
// 每个命令一个 markdown 文件
interface SlashCommand {
  id: string;
  name: string;        // 文件名
  description?: string;
  content: string;     // 文件内容
  allowedTools?: string[];
  model?: ClaudeModel;
}
```

### 5. Agent 定义存储

```typescript
// Agent 定义: ~/.claude/agents/{agentName}/agent.md
// 或: {vault}/.claude/agents/{agentName}/agent.md
interface CustomAgent {
  name: string;        // 目录名
  prompt: string;      // agent.md 内容
  allowedTools: string[];
  model?: string;
}
```

### 6. MCP 配置存储

```typescript
// MCP 配置: {vault}/.claude/mcp.json
interface MCPConfig {
  mcpServers: {
    [serverName]: {
      command?: string;
      args?: string[];
      url?: string;
      transport: 'stdio' | 'sse' | 'http';
      enabled: boolean;
    }
  };
}
```

---

## 数据索引与查询

```mermaid
flowchart TB
    subgraph 内存索引["内存索引"]
        A1[会话列表<br/>Map<string, Conversation>]
        A2[MCP 服务器<br/>Map<string, MCPServer>]
        A3[自定义 Agent<br/>Map<string, Agent>]
        A4[插件列表<br/>Map<string, Plugin>]
    end
    
    subgraph 文件扫描["文件扫描"]
        B1[启动时扫描<br/>~/.claude/]
        B2[扫描 skills/]
        B3[扫描 agents/]
        B4[扫描 plugins/]
        B5[扫描 {vault}/.claude/]
    end
    
    subgraph 查询优化["查询优化"]
        C1[@提及搜索<br/>文件名索引]
        C2[笔记内容搜索<br/>Obsidian 搜索 API]
        C3[历史会话加载<br/>懒加载]
    end
    
    B1 --> B2
    B1 --> B3
    B1 --> B4
    B1 --> B5
    
    B2 --> A1
    B3 --> A3
    B4 --> A4
    B5 --> A2
    
    A1 --> C3
    A3 --> C1
```

> **图注**：Claudian 在启动时会扫描各种配置目录，将配置加载到内存中的 Map 结构，提供快速访问。@ 提及功能使用文件名索引实现快速搜索，笔记内容搜索复用 Obsidian 原生的搜索 API。

---

## 数据安全设计

```mermaid
flowchart LR
    A[数据安全策略] --> B[存储隔离]
    A --> C[敏感信息保护]
    A --> D[访问控制]
    A --> E[数据备份]
    
    B --> B1[会话数据按 vault 隔离]
    B --> B2[设置与 Obsidian 共享存储]
    B --> B3[SDK 数据在用户目录]
    
    C --> C1[API Key 存在环境变量]
    C --> C2[不存储在设置文件中]
    C --> C3[进程内存中短暂存在]
    
    D --> D1[Vault 内文件可读写]
    D --> D2[Vault 外仅白名单路径]
    D --> D3[命令执行黑名单限制]
    
    E --> E1[Obsidian 自动备份]
    E --> E2[SDK 自动持久化]
    E --> E3[会话可导出导入]
    
    style A fill:#e3f2fd
    style B fill:#e8f5e9
    style C fill:#e8f5e9
    style D fill:#e8f5e9
    style E fill:#e8f5e9
```

> **图注**：数据安全从四个维度保障。存储隔离确保不同 vault 的数据不会混淆；敏感信息（API Key）只存在于环境变量中，不在任何配置文件里保存；访问控制通过路径限制和命令黑名单实现；数据备份依赖 Obsidian 和 SDK 的自动持久化机制。
