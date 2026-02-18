# 模块架构图

## 系统整体架构

```mermaid
graph TB
    subgraph 宿主层["宿主层 (Obsidian)"]
        A1[Electron Runtime]
        A2[Obsidian API]
        A3[Vault FileSystem]
        A4[Markdown Editor]
        A5[Command Palette]
    end
    
    subgraph 插件层["插件层 (Claudian)"]
        B1[main.ts 入口]
        B2[ClaudianView 视图]
        B3[ClaudianSettingTab 设置]
    end
    
    subgraph 核心层["核心层 (Core)"]
        C1[ClaudianService<br/>SDK封装]
        C2[SessionManager<br/>会话管理]
        C3[AgentManager<br/>Agent管理]
        C4[McpServerManager<br/>MCP管理]
        C5[PluginManager<br/>插件管理]
        C6[SecurityManager<br/>安全管理]
        C7[StorageService<br/>存储服务]
    end
    
    subgraph 功能层["功能层 (Features)"]
        D1[Chat 聊天模块]
        D2[Inline Edit<br/>行内编辑]
        D3[Settings UI<br/>设置界面]
    end
    
    subgraph 共享层["共享层 (Shared)"]
        E1[Components 组件]
        E2[Modals 弹窗]
        E3[Mention Dropdown<br/>@提及下拉]
        E4[Icons 图标]
    end
    
    subgraph 工具层["工具层 (Utils)"]
        F1[Context 上下文]
        F2[Diff 差异对比]
        F3[Editor 编辑器]
        F4[Path 路径处理]
        F5[Session 会话工具]
    end
    
    subgraph 外部层["外部层 (External)"]
        G1[Anthropic API]
        G2[MCP Servers]
        G3[Claude CLI]
    end
    
    A2 --> B1
    B1 --> C1
    B1 --> D1
    B1 --> D2
    B1 --> E1
    
    C1 --> C2
    C1 --> G1
    C1 --> G3
    C4 --> G2
    
    D1 --> E3
    D1 --> C1
    D2 --> C1
    
    E1 --> E2
    E3 --> E1
    
    F1 --> A3
    F3 --> A4
    
    style G1 fill:#e3f2fd
    style G2 fill:#e8f5e9
    style G3 fill:#fff3e0
```

> **图注**：架构图展示了五层结构。宿主层是 Obsidian 提供的运行环境，插件层是 Claudian 的入口和视图，核心层封装了所有业务逻辑，功能层提供具体功能实现，共享层提供可复用的 UI 组件，工具层提供各种辅助函数，外部层是与第三方服务的集成。

---

## 核心模块架构

```mermaid
flowchart TB
    subgraph AgentModule["Agent 模块"]
        A1[ClaudianService]
        A2[SessionManager]
        A3[MessageChannel]
        A4[QueryOptionsBuilder]
        
        A1 --> A2
        A1 --> A3
        A1 --> A4
    end
    
    subgraph StorageModule["Storage 模块"]
        B1[StorageService]
        B2[VaultFileAdapter]
        B3[SessionStorage]
        B4[SettingsStorage]
        B5[SlashCommandStorage]
        
        B1 --> B2
        B1 --> B3
        B1 --> B4
        B1 --> B5
    end
    
    subgraph SecurityModule["Security 模块"]
        C1[ApprovalManager]
        C2[BlocklistChecker]
        C3[BashPathValidator]
        C4[SecurityHooks]
        
        C1 --> C4
        C2 --> C4
        C3 --> C4
    end
    
    subgraph ExtensionModule["Extension 模块"]
        D1[AgentManager]
        D2[PluginManager]
        D3[McpServerManager]
        D4[SlashCommandManager]
        
        D1 --> D4
        D2 --> D4
    end
    
    subgraph HooksModule["Hooks 模块"]
        E1[PreToolUse Hook]
        E2[PostToolUse Hook]
        E3[VaultRestriction Hook]
        E4[Blocklist Hook]
        
        E1 --> C1
        E1 --> C2
        E3 --> C3
    end
    
    A1 --> B1
    A1 --> C4
    A1 --> D3
    
    C4 --> E1
    C4 --> E2
```

> **图注**：核心层分为五个模块。Agent 模块负责与 AI 通信，Storage 模块负责数据持久化，Security 模块负责安全检查，Extension 模块管理各种扩展，Hooks 模块提供工具调用的拦截点。模块之间通过依赖注入和事件机制协作。

---

## 功能模块架构

### 聊天模块 (Chat)

```mermaid
flowchart TB
    subgraph ChatView["聊天视图"]
        V1[ClaudianView]
        V2[TabManager]
        V3[TabBar]
    end
    
    subgraph Controllers["控制器"]
        C1[ChatController]
        C2[InputController]
        C3[MentionController]
        C4[SlashCommandController]
    end
    
    subgraph Rendering["渲染层"]
        R1[MessageRenderer]
        R2[ToolCallRenderer]
        R3[CodeBlockRenderer]
        R4[DiffRenderer]
    end
    
    subgraph State["状态管理"]
        S1[ConversationState]
        S2[MessageState]
        S3[UIState]
    end
    
    V1 --> V2
    V2 --> V3
    
    V1 --> C1
    C1 --> C2
    C2 --> C3
    C2 --> C4
    
    C1 --> R1
    C1 --> R2
    C1 --> R3
    R3 --> R4
    
    C1 --> S1
    S1 --> S2
    V1 --> S3
```

> **图注**：聊天模块采用 MVC 架构。ClaudianView 是视图层，各种 Controller 处理用户交互，Rendering 层负责消息内容的渲染，State 管理对话状态。这种分层让代码更易维护和测试。

### 行内编辑模块 (Inline Edit)

```mermaid
flowchart TB
    subgraph Trigger["触发层"]
        T1[Editor 选中文本]
        T2[快捷键/命令]
        T3[打开 InlineEditModal]
    end
    
    subgraph Service["服务层"]
        S1[InlineEditService]
        S2[创建冷启动查询]
        S3[流式响应处理]
    end
    
    subgraph UI["UI 层"]
        U1[InlineEditModal]
        U2[原始文本显示]
        U3[建议文本预览]
        U4[差异对比视图]
        U5[操作按钮组]
    end
    
    subgraph Apply["应用层"]
        A1[接受修改]
        A2[拒绝修改]
        A3[重新生成]
        A4[Editor.replaceSelection]
    end
    
    T1 --> T2
    T2 --> T3
    T3 --> U1
    
    U1 --> S1
    S1 --> S2
    S2 --> S3
    S3 --> U3
    
    U3 --> U4
    U4 --> U5
    
    U5 --> A1
    U5 --> A2
    U5 --> A3
    A1 --> A4
    A3 --> S2
```

> **图注**：行内编辑模块的核心是冷启动查询（不依赖会话历史）和差异对比。服务层负责与 AI 通信，UI 层提供实时预览和差异显示，应用层处理用户的接受/拒绝/重试操作。

---

## 请求处理链路

```mermaid
flowchart TD
    A[用户输入] --> B[输入解析]
    
    B --> C{特殊语法?}
    C -->|@提及| D[解析引用]
    C -->|/命令| E[展开命令]
    C -->|#指令| F[更新系统提示]
    C -->|普通文本| G[直接使用]
    
    D --> H[读取引用内容]
    E --> I[加载命令模板]
    F --> J[保存自定义指令]
    
    H --> K[构建上下文]
    I --> K
    G --> K
    
    K --> L{查询类型?}
    L -->|普通查询| M[Persistent Query]
    L -->|行内编辑| N[Cold Start Query]
    L -->|标题生成| O[Cold Start Query]
    
    M --> P[复用现有会话]
    N --> Q[创建临时会话]
    
    P --> R[QueryOptionsBuilder]
    Q --> R
    
    R --> S[构建 QueryOptions]
    S --> T[调用 Agent SDK]
    
    T --> U[Anthropic API]
    
    U --> V{响应类型?}
    V -->|文本流| W[流式更新 UI]
    V -->|工具调用| X[权限检查]
    V -->|完成| Y[保存会话]
    
    X --> Z{检查通过?}
    Z -->|是| AA[执行工具]
    Z -->|否| AB[返回错误]
    
    AA --> AC[返回结果]
    AB --> AD[提示用户]
    AC --> T
    
    W --> V
    Y --> AE[结束]
    AD --> AE
```

> **图注**：请求处理链路展示了从用户输入到 AI 响应的完整流程。关键点包括特殊语法解析、上下文构建、两种查询模式（持久查询和冷启动）、流式响应处理、工具调用权限检查。

---

## 技术栈全景

```mermaid
mindmap
  root((Claudian 技术栈))
    宿主环境
      Obsidian
        Electron
        TypeScript API
        Plugin System
    
    开发语言
      TypeScript
        类型安全
        现代语法
    
    构建工具
      esbuild
        快速编译
        插件系统
    
    核心依赖
      Agent SDK
        @anthropic-ai/claude-agent-sdk
        流式通信
        工具调用
      MCP SDK
        @modelcontextprotocol/sdk
        外部工具集成
    
    UI 组件
      Obsidian 原生
        主题兼容
        原生体验
      自定义组件
        虚拟滚动
        代码高亮
        差异对比
    
    状态管理
      内置状态
        无外部库
        事件驱动
      持久化
        Obsidian 存储 API
        文件系统
    
    安全机制
      权限系统
        ApprovalManager
        BlocklistChecker
      路径验证
        BashPathValidator
    
    扩展机制
      Skills
        Markdown 定义
        自动加载
      Agents
        子代理配置
        工具限制
      MCP
        外部服务
        工具调用
      Plugins
        CLI 兼容
        自动发现
```

> **图注**：技术栈选择遵循"与 Obsidian 深度集成"原则。使用 TypeScript 保证类型安全，esbuild 保证构建速度，Agent SDK 提供 AI 能力，MCP SDK 提供扩展能力。UI 层复用 Obsidian 原生组件保持体验一致。

---

## 模块依赖关系

```mermaid
flowchart TB
    subgraph 高层模块["高层模块"]
        A1[ClaudianView]
        A2[ClaudianSettingTab]
    end
    
    subgraph 核心模块["核心模块"]
        B1[ClaudianService]
        B2[StorageService]
        B3[SecurityManager]
    end
    
    subgraph 子系统["子系统"]
        C1[SessionManager]
        C2[AgentManager]
        C3[McpServerManager]
        C4[PluginManager]
    end
    
    subgraph 基础设施["基础设施"]
        D1[Agent SDK]
        D2[Obsidian API]
        D3[File System]
    end
    
    A1 --> B1
    A1 --> B2
    A2 --> B2
    
    B1 --> C1
    B1 --> C2
    B1 --> C3
    B2 --> C4
    
    B1 --> D1
    B2 --> D2
    B2 --> D3
    B3 --> D2
    
    C1 --> D1
    C3 --> D1
    
    style D1 fill:#e3f2fd
    style D2 fill:#e3f2fd
    style D3 fill:#e3f2fd
```

> **图注**：依赖关系遵循"依赖倒置"原则。高层模块（视图、设置）依赖核心模块（服务、存储、安全），核心模块依赖子系统和基础设施。基础设施（SDK、API、文件系统）在最底层，被上层模块依赖。

---

## 部署架构

```mermaid
flowchart TB
    subgraph UserMachine["用户本地机器"]
        A1[Obsidian App]
        A2[Claudian Plugin]
        A3[Vault Files]
        A4[.claude/ 配置]
    end
    
    subgraph CloudServices["云服务"]
        B1[Anthropic API
            claude.ai]
        B2[Custom Endpoint
            OpenRouter/etc]
    end
    
    subgraph ExternalTools["外部工具"]
        C1[MCP Servers
            GitHub/PostgreSQL/etc]
        C2[Web Services
            APIs]
    end
    
    A1 --> A2
    A2 --> A3
    A2 --> A4
    
    A2 -->|HTTPS| B1
    A2 -->|HTTPS| B2
    
    A2 -->|MCP Protocol| C1
    A2 -->|HTTP| C2
    
    style A1 fill:#e8f5e9
    style A2 fill:#c8e6c9
    style B1 fill:#e3f2fd
    style B2 fill:#e3f2fd
    style C1 fill:#fff3e0
    style C2 fill:#fff3e0
```

> **图注**：Claudian 是纯客户端插件，所有代码在用户本地运行。只与外部的 AI API（Anthropic 或自定义端点）和 MCP 服务器通信。用户数据（笔记、配置）全部保存在本地，符合隐私优先的设计原则。
