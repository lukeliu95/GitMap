# 模块架构图

## 系统整体架构

```mermaid
flowchart TB
    subgraph HostPlatforms["宿主平台层"]
        A1[Claude Code
           Anthropic]
        A2[Cursor
           AI Editor]
        A3[Codex
           OpenAI]
        A4[OpenCode
           Open Source]
    end
    
    subgraph IntegrationLayer["集成层"]
        B1[Claude Code Plugin
           .claude-plugin/]
        B2[Cursor Plugin
           .cursor-plugin/]
        B3[Codex Config
           .codex/INSTALL.md]
        B4[OpenCode Config
           .opencode/INSTALL.md]
    end
    
    subgraph CoreSystem["Superpowers 核心"]
        C1[Skill System
           skills/]
        C2[Command System
           commands/]
        C3[Agent System
           agents/]
        C4[Hook System
           hooks/]
    end
    
    subgraph AISDK["AI SDK 层"]
        D1[Claude Agent SDK
           @anthropic-ai/claude-agent-sdk]
        D2[Tool Use API
           Read/Write/Bash]
        D3[Subagent API
           Dispatch/Review]
        D4[Skill Tool
           Skill Invocation]
    end
    
    subgraph WorkflowEngine["工作流引擎"]
        E1[Workflow Orchestrator]
        E2[Skill Trigger Detector]
        E3[Subagent Dispatcher]
        E4[Review Coordinator]
    end
    
    HostPlatforms --> IntegrationLayer
    IntegrationLayer --> AISDK
    CoreSystem --> AISDK
    AISDK --> WorkflowEngine
    
    style A1 fill:#e3f2fd
    style A2 fill:#e3f2fd
    style A3 fill:#e3f2fd
    style A4 fill:#e3f2fd
    style C1 fill:#c8e6c9
    style C2 fill:#c8e6c9
    style C3 fill:#c8e6c9
    style C4 fill:#c8e6c9
```

> **图注**：架构图展示了四层结构。宿主平台层是 AI 编程助手，集成层提供平台特定的配置，核心系统包含所有技能定义，AI SDK 层提供工具调用和子代理能力，工作流引擎协调整个流程。

---

## 核心模块架构

### 技能系统 (Skill System)

```mermaid
flowchart TB
    subgraph SkillDefinition["技能定义"]
        A1[SKILL.md
           YAML Frontmatter +
           Markdown Content]
        A2[Checklist
           Ordered Tasks]
        A3[Process Flow
           Graphviz DOT]
        A4[Prompt Templates
           Subagent Prompts]
    end
    
    subgraph SkillRegistry["技能注册表"]
        B1[Skill Loader
           Parse Markdown]
        B2[Trigger Index
           Pattern Matching]
        B3[Priority Queue
           Process > Impl]
        B4[Skill Cache
           Loaded Skills]
    end
    
    subgraph SkillExecution["技能执行"]
        C1[Context Builder
           Gather Context]
        C2[Step Executor
           Execute Checklist]
        C3[Validator
           Verify Completion]
        C4[Next Skill Resolver
           Workflow Navigation]
    end
    
    A1 --> B1
    A2 --> B1
    A3 --> B1
    A4 --> B1
    
    B1 --> B2
    B2 --> B3
    B3 --> B4
    
    B4 --> C1
    C1 --> C2
    C2 --> C3
    C3 --> C4
```

> **图注**：技能系统采用"定义-注册-执行"三层架构。技能以 Markdown 文件定义，包含元数据、检查清单、流程图。技能注册表负责加载和索引，执行引擎按检查清单逐步执行。

### 子代理系统 (Subagent System)

```mermaid
flowchart TB
    subgraph SubagentTypes["子代理类型"]
        A1[Implementer
           实现者]
        A2[SpecReviewer
           Spec审查员]
        A3[QualityReviewer
           代码质量审查员]
        A4[FinalReviewer
           最终审查员]
    end
    
    subgraph Dispatcher["调度器"]
        B1[Task Queue
           任务队列]
        B2[Context Isolator
           上下文隔离]
        B3[Prompt Builder
           提示词构建]
        B4[Result Collector
           结果收集]
    end
    
    subgraph ReviewSystem["审查系统"]
        C1[Spec Compliance Check
           设计符合性检查]
        C2[Code Quality Check
           代码质量检查]
        C3[Issue Tracker
           问题追踪]
        C4[Re-review Loop
           重新审查循环]
    end
    
    A1 --> B1
    A2 --> B1
    A3 --> B1
    A4 --> B1
    
    B1 --> B2
    B2 --> B3
    B3 -->|Dispatch| A1
    A1 -->|Return| B4
    
    B4 --> C1
    C1 -->|Issues| C3
    C3 --> C4
    C4 -->|Re-dispatch| A1
    
    C1 -->|Pass| C2
    C2 -->|Issues| C3
    C2 -->|Pass| B4
```

> **图注**：子代理系统采用"派遣-执行-审查-修复"的循环模式。关键是上下文隔离——每个子代理只获得完成任务所需的最小上下文，避免信息过载和上下文污染。

### 命令系统 (Command System)

```mermaid
flowchart LR
    A[用户输入] --> B{匹配命令?}
    
    B -->|/brainstorm| C[Brainstorm Command]
    B -->|/write-plan| D[Write Plan Command]
    B -->|/execute-plan| E[Execute Plan Command]
    B -->|不匹配| F[自然语言处理]
    
    C --> G[加载命令模板]
    D --> G
    E --> G
    
    G --> H[解析参数]
    H --> I[填充模板]
    I --> J[执行命令]
    
    F --> K[技能触发检测]
    K --> L[匹配最佳技能]
    L --> J
    
    J --> M[返回结果]
```

### 钩子系统 (Hook System)

```mermaid
flowchart TB
    subgraph HookPoints["钩子点"]
        A1[pre-skill-execution]
        A2[post-skill-execution]
        A3[pre-subagent-dispatch]
        A4[post-subagent-return]
        A5[pre-review]
        A6[post-review]
    end
    
    subgraph HookRegistry["钩子注册表"]
        B1[Hook Loader]
        B2[Event Router]
        B3[Priority Sorter]
    end
    
    subgraph HookExecution["钩子执行"]
        C1[Before Hooks]
        C2[Main Action]
        C3[After Hooks]
    end
    
    A1 --> B1
    A2 --> B1
    A3 --> B1
    A4 --> B1
    A5 --> B1
    A6 --> B1
    
    B1 --> B2
    B2 --> B3
    
    B3 -->|Trigger| C1
    C1 --> C2
    C2 --> C3
```

---

## 工作流引擎架构

```mermaid
flowchart TB
    subgraph InputLayer["输入层"]
        A1[User Input]
        A2[File Changes]
        A3[Timer Events]
    end
    
    subgraph EventBus["事件总线"]
        B1[Event Router]
        B2[Event Queue]
        B3[Event Handlers]
    end
    
    subgraph StateMachine["状态机"]
        C1[State Manager]
        C2[Transition Rules]
        C3[History Logger]
    end
    
    subgraph ActionLayer["动作层"]
        D1[Skill Invoker]
        D2[Subagent Dispatcher]
        D3[Review Coordinator]
        D4[Output Formatter]
    end
    
    A1 --> B1
    A2 --> B1
    A3 --> B1
    
    B1 --> B2
    B2 --> B3
    
    B3 --> C1
    C1 --> C2
    C2 --> C3
    
    C2 -->|Trigger| D1
    C2 -->|Trigger| D2
    C2 -->|Trigger| D3
    
    D1 --> D4
    D2 --> D4
    D3 --> D4
    
    D4 --> A1
```

> **图注**：工作流引擎采用事件驱动架构。输入层接收各种事件，事件总线路由到对应处理器，状态机管理工作流状态，动作层执行具体的技能和子代理操作。

---

## 技术栈全景

```mermaid
mindmap
  root((Superpowers 技术栈))
    配置格式
      YAML Frontmatter
        元数据定义
        技能触发条件
      Markdown
        技能文档
        命令定义
        提示词模板
      Graphviz DOT
        流程图定义
        可视化工作流
    
    宿主集成
      Claude Code
        Plugin Marketplace
        Skill Tool
      Cursor
        Plugin System
        Agent Chat
      Codex
        Manual Install
        Config-based
      OpenCode
        Manual Install
        Skill Compatible
    
    AI SDK
      Claude Agent SDK
        Tool Use API
        Subagent API
        Streaming
      MCP Protocol
        External Tools
        JSON-RPC 2.0
    
    设计原则
      Markdown-First
        版本控制友好
        社区贡献容易
      Skill-Driven
        强制工作流程
        标准化输出
      Subagent-Isolation
        独立上下文
        避免污染
      Test-Driven
        红绿重构
        强制测试
    
    扩展机制
      Skills
        核心工作流
        自动触发
      Commands
        快捷命令
        手动触发
      Agents
        子代理定义
        审查角色
      Hooks
        工作流扩展
        自定义逻辑
```

> **图注**：Superpowers 的技术栈以 Markdown 为核心，确保可读性和版本控制友好。所有定义都是声明式的，技能系统自动处理执行逻辑。子代理隔离和测试驱动是架构设计的核心原则。

---

## 模块依赖关系

```mermaid
flowchart TB
    subgraph Core["核心模块"]
        A1[Skill System]
        A2[Command System]
        A3[Agent System]
        A4[Hook System]
    end
    
    subgraph Engine["引擎模块"]
        B1[Workflow Engine]
        B2[Subagent Dispatcher]
        B3[Review Coordinator]
    end
    
    subgraph Integration["集成模块"]
        C1[Claude Code Plugin]
        C2[Cursor Plugin]
        C3[Codex Adapter]
        C4[OpenCode Adapter]
    end
    
    subgraph External["外部依赖"]
        D1[Claude Agent SDK]
        D2[MCP SDK]
        D3[Git CLI]
    end
    
    A1 --> B1
    A2 --> B1
    A3 --> B2
    A4 --> B1
    
    B1 --> B2
    B1 --> B3
    B2 --> B3
    
    C1 --> B1
    C2 --> B1
    C3 --> B1
    C4 --> B1
    
    B1 --> D1
    B2 --> D1
    B3 --> D1
    B2 --> D2
    B1 --> D3
    
    style Core fill:#c8e6c9
    style Engine fill:#ffffcc
    style Integration fill:#e3f2fd
```

> **图注**：依赖关系遵循"核心→引擎→集成→外部"的分层原则。核心模块（技能、命令、代理、钩子）是基础定义，引擎模块负责执行，集成模块适配不同平台，外部依赖提供 AI 和工具能力。

---

## 部署架构

```mermaid
flowchart TB
    subgraph Local["本地开发环境"]
        A1[Developer Machine]
        A2[Project Repository]
        A3[Git Worktree]
    end
    
    subgraph AIPlatform["AI 平台"]
        B1[Claude Code CLI]
        B2[Superpowers Plugin]
        B3[Skill Registry]
    end
    
    subgraph Cloud["云服务"]
        C1[Anthropic API
           Claude Models]
        C2[OpenAI API
           Codex]
        C3[MCP Servers
           GitHub/PostgreSQL/etc]
    end
    
    A1 -->|Runs| B1
    A2 -->|Contains| A3
    A3 -->|Isolated Workspace| B1
    
    B1 -->|Loads| B2
    B2 -->|Manages| B3
    B3 -->|Reads| A2
    
    B1 -->|HTTPS + SSE| C1
    B1 -->|HTTPS + SSE| C2
    B1 -->|JSON-RPC 2.0| C3
    
    style A1 fill:#e8f5e9
    style A2 fill:#e8f5e9
    style A3 fill:#c8e6c9
    style B1 fill:#e3f2fd
    style C1 fill:#fff3e0
    style C2 fill:#fff3e0
    style C3 fill:#fff3e0
```

> **图注**：Superpowers 是纯本地系统，所有代码和配置都在开发者本地。AI 平台（Claude Code/Cursor）在本地运行，通过 HTTPS/SSE 连接到云端的 AI 模型 API。MCP 服务器可以本地或远程运行。
