# 项目全景鸟瞰

## 一句话介绍

**Superpowers** 是一套为 Claude Code 和其他 AI 编程助手设计的软件开发工作流系统，通过 14+ 个可组合的技能模块，强制执行规范的开发流程：从设计头脑风暴到 TDD 实现，再到子代理代码审查，确保 AI 在独立开发数小时时仍能保持一致性和代码质量。

---

## 核心价值主张

```mermaid
flowchart LR
    A[开发者痛点] --> B[Superpowers 解决方案]
    B --> C[用户收益]
    
    A1[AI 直接写代码<br/>缺乏设计思考] --> B1[强制头脑风暴流程<br/>先设计后实现]
    A2[AI 生成代码质量<br/>参差不齐] --> B2[TDD + 双阶段审查<br/>Spec + Code Quality]
    A3[AI 容易偏离<br/>项目目标] --> B3[子代理隔离开发<br/>每任务独立上下文]
    A4[AI 难以处理<br/>复杂多步骤任务] --> B4[细粒度计划拆解<br/>2-5分钟每任务]
    A5[AI 缺乏系统性<br/>调试方法] --> B5[四阶段调试流程<br/>根因追踪方法论]
    
    B1 --> C1[减少返工<br/>提升设计质量]
    B2 --> C2[代码可维护<br/>测试覆盖率高]
    B3 --> C3[数小时自主开发<br/>无需干预]
    B4 --> C4[复杂项目可管理<br/>进度可控]
    B5 --> C5[快速定位问题<br/>减少调试时间]
    
    style A fill:#ffcccc
    style C fill:#ccffcc
    style B fill:#ffffcc
```

> **图注**：上图展示了 AI 辅助编程中的核心痛点如何通过 Superpowers 的工作流得到系统性解决。核心价值在于通过强制流程约束，让 AI 的自主开发能力达到" enthusiastic junior engineer "的水平，可以独立工作数小时而不偏离目标。

---

## 产品功能全景图

```mermaid
mindmap
  root((Superpowers))
    核心工作流
      设计阶段
        brainstorming
          苏格拉底式提问
          探索2-3种方案
          分章节呈现设计
          用户验证批准
      准备阶段
        using-git-worktrees
          创建隔离工作区
          新分支开发
          验证干净基线
        writing-plans
          细粒度任务拆解
          2-5分钟每任务
          完整文件路径
          验证步骤明确
      实现阶段
        subagent-driven-development
          每任务新鲜子代理
          实现者开发测试
          Spec审查员验证
          代码质量审查
        executing-plans
          批量任务执行
          人工检查点
          并行会话模式
      质量阶段
        test-driven-development
          RED-GREEN-REFACTOR
          强制测试先行
          删除无测试代码
        requesting-code-review
          计划符合性检查
          严重问题阻断
      收尾阶段
        finishing-a-development-branch
          验证测试通过
          合并/PR决策
          清理工作区
    
    支持技能
      调试技能
        systematic-debugging
          四阶段根因分析
          防御深度检查
          条件等待技术
        verification-before-completion
          修复验证
          确保真正解决
      协作技能
        receiving-code-review
          响应审查反馈
          处理修改建议
        dispatching-parallel-agents
          并发子代理工作流
          并行任务处理
        using-superpowers
          技能系统介绍
          强制使用规则
      元技能
        writing-skills
          创建新技能
          测试方法论
    
    扩展能力
      命令系统
        brainstorm
          快速启动设计
        write-plan
          生成实施计划
        execute-plan
          执行计划任务
      代理定义
        code-reviewer
          代码审查代理
          质量检查标准
      钩子系统
          工作流钩子
          自定义扩展点
    
    目标平台
      Claude Code
          原生插件支持
          Skill工具调用
      Cursor
          插件市场
          Agent聊天
      Codex
          手动安装
          完整功能
      OpenCode
          手动安装
          技能兼容
```

> **图注**：思维导图展示了 Superpowers 的完整技能体系。核心是一个 7 步的标准软件开发工作流（设计→准备→实现→质量→收尾），辅以调试、协作、元技能三类支持技能。所有技能通过命令、代理、钩子系统扩展，支持 Claude Code、Cursor、Codex、OpenCode 四大平台。

---

## 适合人群与使用场景

### 目标用户画像

| 用户类型 | 特征 | 使用场景 | 核心价值 |
|---------|------|---------|---------|
| **AI 原生开发者** | 主要使用 AI 编程助手写代码 | 让 Claude 独立开发新功能 2-4 小时 | AI 自主开发质量可控 |
| **技术团队 Lead** | 管理使用 AI 的团队成员 | 统一团队 AI 开发规范 | 标准化 AI 输出质量 |
| **独立开发者** | 个人项目，追求高效率 | 快速原型到生产代码 | 减少返工，一次做对 |
| **开源项目维护者** | 处理大量 PR 和 Issue | 系统化修复 Bug 和实现功能 | 保持代码库质量一致性 |
| **AI 工具开发者** | 构建 AI 编程工具 | 参考工作流设计方法论 | 最佳实践参考 |

### 典型使用场景

**场景一：从零构建新功能（完整工作流）**

> 小李是一个使用 Claude Code 的全栈开发者。他需要为 SaaS 产品添加一个"团队协作"功能。他输入："帮我实现团队协作功能，支持邀请成员、权限管理、活动日志"。

> **brainstorming** 技能激活：
> - Claude 先检查了项目现有架构和代码库
> - 通过 5 轮苏格拉底式提问，澄清了权限模型、邀请流程、日志保留策略
> - 提出了 3 种方案：简单邀请码、邮件邀请+确认、SSO 集成
> - 小李选择了邮件邀请方案
> - 生成了设计文档：数据库 schema、API 设计、UI 流程

> **writing-plans** 技能激活：
> - 将设计拆解为 23 个细粒度任务
> - 每个任务 2-5 分钟：写测试→运行失败→实现→运行通过→提交
> - 明确了每个任务的文件路径和代码示例

> **subagent-driven-development** 技能激活：
> - 在独立 git worktree 中开始开发
> - 每个任务派遣新鲜子代理：
>   - 实现者：编写代码和测试
>   - Spec 审查员：验证符合设计文档
>   - 代码质量审查员：检查代码风格和最佳实践
> - 4 小时后，23 个任务全部完成

> **finishing-a-development-branch** 技能激活：
> - 验证所有测试通过
> - 生成 PR 描述
> - 清理工作区

> 小李只花了 30 分钟参与（回答问题、批准设计），AI 独立完成了 4 小时的开发工作，代码质量符合团队标准。

**场景二：系统性 Bug 修复**

> 小王的项目出现了一个难以复现的竞态条件 Bug。他输入："用户报告偶尔数据丢失，怀疑是并发问题"。

> **systematic-debugging** 技能激活：
> - **阶段1：信息收集** - 检查日志、复现步骤、环境信息
> - **阶段2：根因假设** - 提出 3 个可能原因：数据库连接池、缓存失效、事务边界
> - **阶段3：实验验证** - 设计实验逐一排除
> - **阶段4：修复验证** - 确定是事务边界问题，编写修复

> **test-driven-development** 技能激活：
> - 先写测试复现竞态条件
> - 运行测试确认失败
> - 实现修复
> - 运行测试确认通过
> - 运行 100 次测试验证稳定性

> Bug 在 1 小时内系统性地解决，而不是凭直觉尝试各种方案。

---

## 技术栈概览

```mermaid
flowchart TB
    subgraph HostPlatforms["宿主平台"]
        A1[Claude Code
           Anthropic官方]
        A2[Cursor
           AI编辑器]
        A3[Codex
           OpenAI]
        A4[OpenCode
           开源AI编辑器]
    end
    
    subgraph SuperpowersSystem["Superpowers 系统"]
        B1[Skill 定义文件
           Markdown格式]
        B2[Command 命令
           Markdown格式]
        B3[Agent 代理定义
           Markdown格式]
        B4[Hooks 钩子
           工作流扩展点]
    end
    
    subgraph AISDK["AI SDK"]
        C1[Claude Agent SDK
           @anthropic-ai/claude-agent-sdk]
        C2[工具调用系统
           Tool Use]
        C3[子代理系统
           Subagent]
        C4[技能调用
           Skill Tool]
    end
    
    subgraph WorkflowEngine["工作流引擎"]
        D1[技能触发器
           关键词/上下文检测]
        D2[流程控制器
           状态机驱动]
        D3[子代理调度器
           创建/销毁/通信]
        D4[审查系统
           双阶段质量门]
    end
    
    HostPlatforms --> AISDK
    SuperpowersSystem --> AISDK
    AISDK --> WorkflowEngine
    
    style A1 fill:#e3f2fd
    style A2 fill:#e3f2fd
    style A3 fill:#e3f2fd
    style A4 fill:#e3f2fd
    style B1 fill:#c8e6c9
    style B2 fill:#c8e6c9
    style B3 fill:#c8e6c9
```

> **图注**：架构图展示了三层结构。Superpowers 作为技能定义层，通过 AI SDK（Claude Agent SDK）与宿主平台集成。工作流引擎负责技能触发、流程控制和子代理调度。所有技能以 Markdown 格式定义，便于版本控制和社区贡献。

---

## 核心数据流转

```mermaid
sequenceDiagram
    actor U as 用户
    participant H as 宿主平台
    participant S as Superpowers技能
    participant A as AI助手
    participant Sub as 子代理
    
    U->>H: 1. 输入开发需求
    H->>A: 2. 检测任务类型
    A->>S: 3. 触发 brainstorming 技能
    
    S->>A: 4. 加载 SKILL.md
    A->>H: 5. 读取项目上下文
    H-->>A: 6. 返回代码库信息
    
    A->>U: 7. 提问澄清需求
    U-->>A: 8. 回答问题
    
    A->>U: 9. 呈现设计方案
    U-->>A: 10. 批准设计
    
    A->>H: 11. 保存 design.md
    A->>S: 12. 触发 writing-plans 技能
    S->>A: 13. 生成实施计划
    A->>H: 14. 保存 plan.md
    
    A->>S: 15. 触发 subagent-driven-dev
    S->>A: 16. 提取所有任务
    
    loop 每个任务
        A->>Sub: 17. 派遣实现者子代理
        Sub->>Sub: 18. 开发+测试+提交
        Sub-->>A: 19. 返回结果
        
        A->>Sub: 20. 派遣 Spec 审查员
        Sub->>Sub: 21. 验证符合设计
        Sub-->>A: 22. 审查结果
        
        alt Spec 不通过
            A->>Sub: 23. 修复问题
            Sub-->>A: 24. 重新审查
        end
        
        A->>Sub: 25. 派遣代码质量审查员
        Sub->>Sub: 26. 代码质量检查
        Sub-->>A: 27. 审查结果
        
        alt 质量不通过
            A->>Sub: 28. 修复问题
            Sub-->>A: 29. 重新审查
        end
    end
    
    A->>S: 30. 触发 finishing-a-branch
    S->>A: 31. 完成分支处理
    A->>H: 32. 生成 PR/合并
    A-->>U: 33. 任务完成
```

> **图注**：时序图展示了完整工作流的数据流转。关键是每个阶段都触发对应的技能，子代理驱动的开发阶段使用"实现-审查-修复"的循环确保质量。每个子代理都有独立的上下文，避免上下文污染。

---

## 产品差异化优势

与直接使用 AI 编程助手相比，Superpowers 的独特价值：

| 维度 | 直接使用 AI | Superpowers |
|------|-----------|-------------|
| **设计阶段** | AI 直接开始写代码 | 强制头脑风暴，先设计后实现 |
| **测试策略** | 事后补测试 | 强制 TDD，测试先行 |
| **代码审查** | 无或人工审查 | 双阶段自动审查（Spec + Quality） |
| **任务拆解** | AI 自行决定步骤 | 细粒度计划，2-5分钟每任务 |
| **复杂任务** | 容易偏离或卡住 | 子代理隔离，每任务独立 |
| **调试方法** | 凭直觉尝试 | 系统化四阶段调试 |
| **工作流** | 随意 | 标准化、可重复、可扩展 |
| **自主时长** | 10-20 分钟需要干预 | 2-4 小时独立工作 |

---

## 下一步阅读建议

- 📖 **[01-用户旅程地图](01-user-journey.md)** — 了解开发者如何使用 Superpowers 完成项目
- 📖 **[02-核心功能详解](02-core-features.md)** — 深入了解每个技能的运作机制
- 📖 **[03-数据关系图](03-data-model.md)** — 查看技能定义和工作流数据结构
- 📖 **[04-模块架构图](04-module-architecture.md)** — 了解系统技术架构
- 📖 **[05-API交互与集成](05-api-interactions.md)** — 查看与 AI 平台的集成方式
