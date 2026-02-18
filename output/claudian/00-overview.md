# 项目全景鸟瞰

## 一句话介绍

**Claudian** 是一款将 Claude Code AI 助手深度集成到 Obsidian 笔记软件中的插件，让用户可以在笔记工作流中直接调用 AI 进行文件读写、代码编辑、搜索分析和多步骤自动化任务。

---

## 核心价值主张

```mermaid
flowchart LR
    A[用户痛点] --> B[我们的解决方案]
    B --> C[用户收益]
    
    A1[在笔记和AI工具间<br/>频繁切换] --> B1[AI直接嵌入<br/>Obsidian侧边栏]
    A2[AI无法访问<br/>本地笔记内容] --> B2[笔记库成为AI<br/>工作目录]
    A3[重复性编辑任务<br/>耗时费力] --> B3[AI自动读写<br/>批量处理文件]
    A4[需要编写代码<br/>分析笔记数据] --> B4[AI执行bash命令<br/>搜索和分析]
    
    B1 --> C1[工作流无缝衔接<br/>无需切换窗口]
    B2 --> C2[AI理解全部上下文<br/>回答更精准]
    B3 --> C3[10倍提升<br/>笔记整理效率]
    B4 --> C4[零基础也能<br/>自动化处理笔记]
    
    style A fill:#ffcccc
    style C fill:#ccffcc
    style B fill:#ffffcc
```

> **图注**：上图展示了知识工作者的核心痛点如何通过 Claudian 得到解决。核心价值在于打破笔记软件与 AI 工具之间的壁垒，让 AI 真正成为笔记工作流的一部分。

---

## 产品功能全景图

```mermaid
mindmap
  root((Claudian))
    核心交互
      侧边栏聊天
        多标签会话
        实时流式响应
        代码高亮显示
      行内编辑
        选中文本直接编辑
        单词级差异预览
        只读工具访问
    
    上下文管理
      自动上下文
        当前聚焦笔记
        编辑器选中文本
      @提及系统
        引用其他笔记
        MCP服务器
        自定义Agent
        外部文件夹
      图片支持
        拖放上传
        粘贴图片
        路径引用
      外部目录
        添加库外文件夹
        跨库引用文件
    
    AI能力
      文件操作
        读取笔记
        创建新笔记
        编辑现有笔记
        批量修改
      搜索分析
        全文搜索
        正则匹配
        内容分析
      Bash命令
        执行终端命令
        脚本自动化
        权限控制
      规划模式
        Shift+Tab切换
        AI先规划后执行
        方案可审核
    
    扩展系统
      Skills技能
        ~/.claude/skills/
        自动识别触发
        复用能力模块
      自定义Agent
        ~/.claude/agents/
        专用子代理
        工具限制配置
      Slash命令
        /快捷指令
        参数占位符
        提示词模板
      MCP支持
        外部工具集成
        上下文保存模式
        @激活服务
      Claude Code插件
        CLI插件兼容
        自动发现加载
        库级配置
    
    安全控制
      权限模式
        YOLO自动执行
        Safe需要确认
        Plan规划模式
      命令黑名单
        rm -rf等危险命令
        正则匹配拦截
        平台特定规则
      路径限制
        笔记库隔离
        导出路径白名单
        符号链接安全检查
    
    模型配置
      模型选择
        Haiku快速
        Sonnet平衡
        Opus强力
        自定义模型
      高级选项
        Thinking预算
        1M上下文窗口
        环境变量配置
        API端点自定义
```

> **图注**：思维导图展示了 Claudian 的六大功能模块：核心交互、上下文管理、AI能力、扩展系统、安全控制和模型配置。每个模块下又细分具体功能点，构成完整的 AI 辅助笔记工作流。

---

## 适合人群与使用场景

### 目标用户画像

| 用户类型 | 特征 | 使用场景 | 核心价值 |
|---------|------|---------|---------|
| **知识管理专家** | 重度 Obsidian 用户，笔记量大 | 用 AI 整理标签、生成目录、批量格式化 | 自动化繁琐的笔记维护工作 |
| **程序员/开发者** | 使用 Obsidian 记录代码笔记 | 让 AI 分析代码片段、生成文档、重构笔记 | 在笔记中直接获得 AI 编程辅助 |
| **学术研究者** | 管理大量文献笔记 | AI 总结论文、提取关键信息、关联相关笔记 | 加速文献综述和知识整合 |
| **内容创作者** | 用 Obsidian 管理创作素材 | AI 根据笔记生成文章大纲、扩展思路 | 从碎片笔记到完整内容 |
| **项目经理** | 用 Obsidian 管理项目文档 | AI 生成会议纪要、追踪任务状态、更新进度 | 自动化项目文档维护 |

### 典型使用场景

**场景一：批量整理笔记标签**

> 小王是一个使用 Obsidian 3 年的知识管理爱好者，积累了 500+ 篇笔记。他打开 Claudian 侧边栏，输入："帮我把所有没有标签的笔记加上合适的标签，根据内容分类为'#学习'、'#工作'、'#灵感'三类"。AI 开始逐个读取笔记内容，分析主题，自动添加标签。10分钟后，所有笔记都被正确分类。小王感叹："以前要花一整天的工作，现在喝杯咖啡就完成了。"

**场景二：基于笔记生成周报**

> 李经理每天在项目会议后都会用 Obsidian 记录要点。周五下午，他选中本周的所有会议笔记，使用行内编辑功能，输入："根据这些笔记生成一份周报，包含：1）本周完成的工作 2）遇到的问题 3）下周计划"。AI 分析所有笔记内容，生成了一份结构清晰的周报。李经理微调后直接发送给上级，节省了 1 小时的整理时间。

**场景三：跨笔记知识问答**

> 张博士正在写论文综述，需要回顾之前读过的几十篇文献笔记。他在 Claudian 中输入："我在笔记中记录了哪些关于'深度学习在医疗影像中的应用'的研究？请总结主要方法和发展趋势"。AI 自动搜索整个笔记库，找到相关笔记，提取关键信息，生成了一份详细的综述摘要。张博士发现了一篇之前忽略的重要论文，完善了文献回顾。

---

## 技术栈概览

```mermaid
flowchart TB
    subgraph 宿主环境["宿主环境 (Obsidian)"]
        A1[Electron App]
        A2[Workspace API]
        A3[Vault 文件系统]
        A4[Editor 编辑器]
    end
    
    subgraph 插件层["插件层 (TypeScript)"]
        B1[main.ts 入口]
        B2[ClaudianView 视图]
        B3[Settings 配置]
    end
    
    subgraph 核心层["核心层 (Core)"]
        C1[ClaudianService<br/>SDK封装]
        C2[AgentManager<br/>Agent管理]
        C3[McpServerManager<br/>MCP服务]
        C4[PluginManager<br/>插件管理]
        C5[SecurityManager<br/>安全管理]
    end
    
    subgraph 功能层["功能层 (Features)"]
        D1[Chat 聊天]
        D2[Inline Edit<br/>行内编辑]
    end
    
    subgraph 外部服务["外部服务"]
        E1[Anthropic API<br/>Claude模型]
        E2[MCP Servers<br/>外部工具]
        E3[Claude CLI<br/>原生集成]
    end
    
    A1 --> B1
    B1 --> C1
    C1 --> E1
    C3 --> E2
    C1 --> E3
    
    B1 --> D1
    B1 --> D2
    
    style E1 fill:#e3f2fd
    style E2 fill:#e8f5e9
    style E3 fill:#fff3e0
```

> **图注**：架构图展示了四层结构。Claudian 作为 Obsidian 插件运行在 Electron 环境中，通过 Obsidian API 与编辑器、文件系统交互。核心层封装了 Claude Agent SDK，负责与 Anthropic API 通信。功能层提供聊天和行内编辑两种交互方式。

---

## 核心数据流转

```mermaid
sequenceDiagram
    actor U as 用户
    participant O as Obsidian
    participant P as Claudian插件
    participant S as ClaudianService
    participant SDK as Agent SDK
    participant API as Anthropic API
    
    U->>O: 1. 打开侧边栏
    O->>P: 2. 激活ClaudianView
    P->>S: 3. 初始化服务
    
    U->>P: 4. 输入消息 + @引用笔记
    P->>P: 5. 解析@提及
    P->>O: 6. 读取引用的笔记内容
    O-->>P: 7. 返回笔记内容
    
    P->>S: 8. 发送消息(含上下文)
    S->>SDK: 9. 调用query()
    SDK->>API: 10. HTTP请求
    
    loop 流式响应
        API-->>SDK: 11. SSE数据流
        SDK-->>S: 12. 流式回调
        S-->>P: 13. 更新UI
        P-->>U: 14. 显示响应
    end
    
    alt 需要工具调用
        API-->>SDK: 15. tool_use请求
        SDK-->>S: 16. 工具调用事件
        S->>P: 17. 请求权限
        P->>U: 18. 显示确认对话框
        U->>P: 19. 确认执行
        P->>O: 20. 执行工具操作
        O-->>P: 21. 返回结果
        P->>S: 22. 发送工具结果
        S->>SDK: 23. 继续对话
    end
```

> **图注**：时序图展示了从用户输入到 AI 响应的完整数据流。核心特点是支持流式响应和工具调用。当 AI 需要操作文件或执行命令时，会暂停响应请求用户确认（Safe 模式下），确保安全性。

---

## 产品差异化优势

与传统 AI 助手或 Obsidian 插件相比，Claudian 的独特价值：

| 维度 | 传统AI助手 | 其他Obsidian AI插件 | Claudian |
|------|-----------|-------------------|---------|
| **集成深度** | 独立应用，需要复制粘贴 | 仅能读取当前笔记 | 完整 Agent 能力，可读写所有笔记 |
| **上下文理解** | 需要手动提供上下文 | 仅当前笔记内容 | 自动关联@提及笔记+选中文本 |
| **文件操作** | 不能直接操作本地文件 | 只读，无法编辑 | 完整的读写编辑能力 |
| **扩展性** | 固定功能 | 有限扩展 | Skills + Agent + MCP + 插件 |
| **命令执行** | 无法执行 | 不支持 | 可控的 Bash 命令执行 |
| **规划能力** | 直接执行 | 无 | Plan 模式：先规划后执行 |

---

## 下一步阅读建议

- 📖 **[01-用户旅程地图](01-user-journey.md)** — 了解用户如何一步步使用产品
- 📖 **[02-核心功能详解](02-core-features.md)** — 深入了解每个功能的运作机制
- 📖 **[03-数据关系图](03-data-model.md)** — 查看系统如何组织和存储数据
- 📖 **[04-模块架构图](04-module-architecture.md)** — 了解系统技术架构
- 📖 **[05-API交互与集成](05-api-interactions.md)** — 查看接口设计和外部集成
