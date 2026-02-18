# 用户旅程地图

## 主用户角色：AI 原生开发者

```mermaid
journey
    title 开发者使用 Superpowers 的完整体验旅程
    section 发现与安装
      在 GitHub 发现项目: 5: 开发者
      阅读 README 了解价值: 4: 开发者
      选择平台安装: 3: 开发者
      安装到 Claude Code: 4: 开发者
      验证安装成功: 5: 开发者
    section 首次使用
      提出开发需求: 5: 开发者
      AI 自动触发 brainstorming: 5: 开发者
      回答问题澄清需求: 4: 开发者
      查看并批准设计方案: 5: 开发者
      看到生成实施计划: 5: 开发者
    section 深度使用
      观察 AI 自主开发: 5: 开发者
      收到 Spec 审查报告: 4: 开发者
      收到 Code Quality 报告: 4: 开发者
      看到任务逐一完成: 5: 开发者
      收到完成总结: 5: 开发者
    section 进阶应用
      自定义技能: 4: 开发者
      使用 TDD 修复 Bug: 5: 开发者
      使用系统化调试: 5: 开发者
      并行子代理任务: 4: 开发者
      分享技能给团队: 5: 开发者
```

> **图注**：用户旅程图展示了开发者从发现到深度使用的过程。评分显示在"深度使用"阶段体验最好（AI 自主开发），而安装和自定义技能阶段有一定门槛。核心惊喜点在于看到 AI 能够独立高质量地工作数小时。

---

## 用户决策流程

```mermaid
flowchart TD
    A[开发者有开发需求] --> B{是否使用 Superpowers?}
    
    B -->|是| C[选择宿主平台]
    B -->|否| D[直接使用 AI 助手]
    
    C --> E{平台类型?}
    E -->|Claude Code| F[安装插件]
    E -->|Cursor| G[从市场安装]
    E -->|Codex/OpenCode| H[手动安装]
    
    F --> I[验证安装]
    G --> I
    H --> I
    
    I --> J{安装成功?}
    J -->|是| K[开始使用]
    J -->|否| L[查看故障排除]
    L --> C
    
    D --> M[AI 直接响应]
    M --> N{结果满意?}
    N -->|是| O[完成]
    N -->|否| P[重新提问]
    P --> M
    
    K --> Q[输入需求]
    Q --> R[AI 检测触发技能]
    
    R --> S{需求类型?}
    S -->|新功能| T[触发 brainstorming]
    S -->|Bug修复| U[触发 systematic-debugging]
    S -->|代码优化| V[触发 requesting-code-review]
    S -->|实现计划| W[触发 writing-plans]
    
    T --> X[进入设计阶段]
    U --> Y[进入调试阶段]
    V --> Z[进入审查阶段]
    W --> AA[进入计划阶段]
    
    X --> AB{设计批准?}
    AB -->|批准| AC[触发 writing-plans]
    AB -->|修改| AD[修订设计]
    AD --> X
    
    AC --> AE[触发 subagent-driven-dev]
    Y --> AE
    AA --> AE
    
    AE --> AF[进入开发阶段]
    AF --> AG{开发模式?}
    
    AG -->|子代理驱动| AH[派遣实现者子代理]
    AG -->|批量执行| AI[批量处理任务]
    AG -->|手动执行| AJ[开发者自行实现]
    
    AH --> AK[Spec 审查]
    AK --> AL{符合设计?}
    AL -->|否| AM[修复问题]
    AM --> AK
    AL -->|是| AN[代码质量审查]
    
    AN --> AO{质量通过?}
    AO -->|否| AP[修复问题]
    AP --> AN
    AO -->|是| AQ[标记任务完成]
    
    AQ --> AR{还有任务?}
    AR -->|是| AH
    AR -->|否| AS[最终代码审查]
    
    AI --> AT[人工检查点]
    AT --> AU{继续?}
    AU -->|是| AI
    AU -->|否| AV[暂停]
    
    AS --> AW[触发 finishing-a-branch]
    AW --> AX[分支收尾]
    AX --> AY{操作?}
    AY -->|合并| AZ[合并到主分支]
    AY -->|PR| BA[创建 Pull Request]
    AY -->|保持| BB[保留分支]
    AY -->|丢弃| BC[删除分支]
    
    style K fill:#c8e6c9
    style X fill:#ffffcc
    style AF fill:#ffffcc
    style AX fill:#c8e6c9
```

> **图注**：决策树展示了开发者使用 Superpowers 的完整路径。核心分支在于需求类型（触发不同技能）和开发模式（子代理驱动/批量执行/手动）。每个关键节点都有明确的质量检查点，确保流程的严谨性。

---

## 关键操作的前后端交互

### 1. brainstorming 技能完整流程

```mermaid
sequenceDiagram
    actor U as 开发者
    participant A as AI助手
    participant S as brainstorming技能
    participant P as 项目代码库
    
    U->>A: 1. 输入需求："实现团队协作功能"
    A->>S: 2. 触发 brainstorming 技能
    
    S->>A: 3. 加载 SKILL.md
    Note over A: 获取技能定义和流程
    
    A->>P: 4. 读取项目上下文
    P-->>A: 5. 返回：架构、技术栈、现有代码
    
    A->>U: 6. 问题1："团队规模预计多大？"
    U-->>A: 7. 回答："10-50人"
    
    A->>U: 8. 问题2："权限模型需求？"
    U-->>A: 9. 回答："Admin/Member/Viewer"
    
    A->>U: 10. 问题3："邀请方式偏好？"
    U-->>A: 11. 回答："邮件邀请"
    
    A->>A: 12. 分析需求并设计方案
    
    A->>U: 13. 呈现方案A：简单邀请码
    A->>U: 14. 呈现方案B：邮件邀请
    A->>U: 15. 推荐方案B及理由
    
    U-->>A: 16. 选择方案B
    
    A->>U: 17. 呈现架构设计（章节1）
    U-->>A: 18. 确认章节1
    
    A->>U: 19. 呈现数据库设计（章节2）
    U-->>A: 20. 确认章节2
    
    A->>U: 21. 呈现API设计（章节3）
    U-->>A: 22. 确认章节3
    
    A->>P: 23. 保存 design.md
    A->>U: 24. 设计完成，触发 writing-plans
```

> **图注**：brainstorming 流程通过 5-8 轮对话逐步澄清需求，提出 2-3 种方案供选择，然后分章节呈现详细设计，每章都需要用户确认。这种渐进式验证确保最终设计符合用户预期。

### 2. subagent-driven-development 流程

```mermaid
sequenceDiagram
    actor U as 开发者
    participant M as 主AI
    participant S as subagent-driven-dev技能
    participant I as 实现者子代理
    participant SR as Spec审查员子代理
    participant CR as 代码质量审查员
    participant P as 项目代码库
    
    U->>M: 1. 开始执行计划
    M->>S: 2. 触发技能
    
    S->>P: 3. 读取 plan.md
    P-->>S: 4. 返回计划内容
    
    S->>M: 5. 提取所有任务（15个）
    M->>M: 6. 创建 TodoWrite 列表
    
    loop 任务1：数据库迁移
        M->>I: 7. 派遣实现者
        Note over M,I: 提供：任务全文+上下文
        
        I->>M: 8. 提问："需要支持回滚吗？"
        M->>U: 9. 转发问题
        U-->>M: 10. 回答："需要"
        M-->>I: 11. 传递回答
        
        I->>I: 12. 编写迁移文件
        I->>I: 13. 编写测试
        I->>P: 14. 运行测试
        P-->>I: 15. 测试结果：PASS
        I->>I: 16. 自我审查
        I->>P: 17. 提交代码
        I-->>M: 18. 返回：任务完成
        
        M->>SR: 19. 派遣 Spec 审查员
        SR->>P: 20. 读取实现代码
        SR->>SR: 21. 对比设计文档
        SR-->>M: 22. 报告：✅ 符合设计
        
        M->>CR: 23. 派遣代码质量审查员
        CR->>P: 24. 读取代码和提交
        CR->>CR: 25. 分析代码质量
        CR-->>M: 26. 报告：✅ 质量通过
        
        M->>M: 27. 标记任务1完成
    end
    
    loop 任务2-14（类似流程，略）
        M->>I: 派遣实现者
        I-->>M: 返回结果
        M->>SR: 审查
        SR-->>M: 报告
        M->>CR: 审查
        CR-->>M: 报告
        M->>M: 标记完成
    end
    
    M->>CR: 28. 派遣最终审查员
    CR->>P: 29. 审查整个实现
    CR-->>M: 30. 报告：所有需求已满足
    
    M->>U: 31. 所有任务完成，准备收尾
```

> **图注**：子代理驱动开发是 Superpowers 的核心创新。每个任务经历"实现→Spec审查→质量审查"三重检查，审查不通过会循环修复。实现者可以提问获取上下文，确保理解正确。这种"fresh context per task"模式避免了上下文污染，让 AI 能持续高质量工作数小时。

### 3. systematic-debugging 流程

```mermaid
sequenceDiagram
    actor U as 开发者
    participant A as AI助手
    participant S as systematic-debugging技能
    participant P as 项目代码库
    
    U->>A: 1. 报告 Bug："偶尔数据丢失"
    A->>S: 2. 触发调试技能
    
    Note over A,S: 阶段1：信息收集
    S->>A: 3. 列出需要收集的信息
    
    A->>U: 4. 提问1："错误发生频率？"
    U-->>A: 5. 回答："大约10%的请求"
    
    A->>U: 6. 提问2："有错误日志吗？"
    U-->>A: 7. 回答："没有错误，但数据不完整"
    
    A->>P: 8. 检查相关代码
    P-->>A: 9. 返回代码
    
    A->>P: 10. 检查最近提交
    P-->>A: 11. 返回提交历史
    
    Note over A,S: 阶段2：根因假设
    A->>A: 12. 分析信息
    
    A->>U: 13. 提出假设1："数据库连接池超时"
    A->>U: 14. 提出假设2："事务未正确提交"
    A->>U: 15. 提出假设3："并发写入冲突"
    
    Note over A,S: 阶段3：实验验证
    A->>A: 16. 设计验证实验
    
    A->>P: 17. 实验1：检查连接池配置
    P-->>A: 18. 结果：配置正常
    
    A->>P: 19. 实验2：添加事务日志
    P-->>A: 20. 结果：发现事务回滚
    
    A->>A: 21. 确定根因：事务边界问题
    
    Note over A,S: 阶段4：修复验证
    A->>S: 22. 触发 TDD 技能
    S->>A: 23. 编写复现测试
    A->>P: 24. 运行测试
    P-->>A: 25. 测试失败（复现成功）
    
    A->>A: 26. 实现修复
    A->>P: 27. 运行测试
    P-->>A: 28. 测试通过
    
    A->>P: 29. 运行压力测试（100次）
    P-->>A: 30. 全部通过
    
    A->>U: 31. 修复完成，根因是事务边界
```

> **图注**：系统化调试采用科学方法（信息收集→假设→验证→修复），避免凭直觉尝试。四阶段流程确保找到的根因是正确的，并通过测试验证修复确实有效。这比随机尝试节省大量时间。

---

## 用户情绪曲线

```mermaid
xychart-beta
    title "开发者情绪值变化曲线"
    x-axis [发现项目, 安装配置, 首次提问, 设计对话, 批准设计, AI自主开发, 收到审查报告, 看到完成, 自定义技能]
    y-axis "情绪值" 0 --> 10
    line [8, 6, 8, 7, 8, 9, 7, 10, 7]
```

**情绪节点解读：**

| 阶段 | 情绪值 | 原因 | 优化方向 |
|------|--------|------|---------|
| 发现项目 | 8 | 被"AI 数小时自主开发"吸引 | - |
| 安装配置 | 6 | 不同平台安装方式不同，Codex/OpenCode 较复杂 | 统一安装体验 |
| 首次提问 | 8 | AI 没有立即写代码，而是提问，感觉专业 | - |
| 设计对话 | 7 | 需要回答多个问题，有些繁琐 | 优化提问质量 |
| 批准设计 | 8 | 看到完整设计方案，心里有底 | - |
| AI 自主开发 | 9 | 看到 AI 独立工作，自己喝咖啡即可 | - |
| 收到审查报告 | 7 | 报告较技术化，可读性一般 | 优化报告格式 |
| 看到完成 | 10 | 代码质量高，测试完善，惊喜 | - |
| 自定义技能 | 7 | 需要学习技能编写规范 | 提供更简单的模板 |

---

## 用户触点清单

```mermaid
flowchart LR
    subgraph Discovery["发现触点"]
        A1[GitHub 仓库]
        A2[博客文章]
        A3[社交媒体]
        A4[朋友推荐]
    end
    
    subgraph Installation["安装触点"]
        B1[Claude Code 插件市场]
        B2[Cursor 插件市场]
        B3[Codex 手动安装]
        B4[OpenCode 手动安装]
    end
    
    subgraph Interaction["交互触点"]
        C1[AI 对话界面]
        C2[设计文档]
        C3[实施计划]
        C4[代码审查报告]
        C5[TodoWrite 列表]
        C6[Skill 定义文件]
    end
    
    subgraph Configuration["配置触点"]
        D1[~/.claude/superpowers/]
        D2[skills/ 目录]
        D3[commands/ 目录]
        D4[agents/ 目录]
        D5[hooks/ 目录]
    end
    
    A1 --> B1
    A1 --> B2
    A1 --> B3
    
    B1 --> C1
    B2 --> C1
    B3 --> C1
    
    C1 --> C2
    C2 --> C3
    C3 --> C4
    C1 --> C5
    
    C4 --> D1
    D1 --> D2
    D1 --> D3
    D1 --> D4
    D1 --> D5
```

> **图注**：触点地图展示了开发者与 Superpowers 交互的所有接触点。核心交互在 AI 对话界面完成，设计文档、计划、审查报告是主要的文档触点。高级用户可以通过修改 skills、commands、agents、hooks 目录来自定义工作流。

---

## 典型用户故事

### 故事一：全栈开发者小李的复杂功能实现

**背景**：小李是独立开发者，使用 Claude Code + Superpowers 开发 SaaS 产品。

**需求**：需要实现一个实时协作编辑功能，类似 Google Docs 的多人同时编辑。

**周一上午** - *启动项目*

小李打开终端，启动 Claude Code：

```
小李：我想实现实时协作编辑功能，支持多人同时编辑文档，需要冲突解决和光标同步。
```

Claude 没有立即写代码，而是触发了 **brainstorming** 技能：

```
Claude：我来帮你设计这个功能。首先让我了解一下项目现状...
[检查项目架构]

Claude：我看到你使用的是 Node.js + Express + MongoDB。对于实时协作，我有几个问题：

1. 预计最大并发用户数是多少？（10人/100人/1000人？）
小李：10-50人

2. 冲突解决策略偏好？（最后写入获胜/操作转换/状态转换）
小李：操作转换（OT）

3. 需要支持离线编辑后同步吗？
小李：第一期不需要
```

经过 6 轮问答，Claude 提出了 3 种方案，小李选择了基于 Socket.io + OT.js 的方案。

**周一下午** - *设计阶段*

Claude 分章节呈现了详细设计：
- 章节1：架构设计（WebSocket 连接管理、OT 算法集成）
- 章节2：数据模型（Document、Operation、Cursor 集合）
- 章节3：API 设计（REST + WebSocket 事件）
- 章节4：冲突解决算法

小李逐章确认后，Claude 保存了 `docs/plans/2024-01-15-collaborative-editing-design.md`。

**周二** - *计划阶段*

Claude 触发 **writing-plans** 技能，生成了 28 个细粒度任务：
- Task 1: 安装 socket.io 依赖
- Task 2: 创建 OT 算法模块
- Task 3: 编写 OT 算法测试
- ...
- Task 28: 集成测试

每个任务都包含：文件路径、代码示例、测试命令、验证步骤。

**周三-周四** - *开发阶段*

Claude 触发 **subagent-driven-development** 技能，开始自主开发。

小李看着终端滚动：
```
[Task 1/28] 派遣实现者子代理...
[Task 1/28] 实现者完成：安装依赖
[Task 1/28] Spec 审查员：✅ 符合设计
[Task 1/28] 代码质量审查员：✅ 质量通过
[Task 1/28] 完成 ✓

[Task 2/28] 派遣实现者子代理...
...
```

每隔 30 分钟，小李看一眼进度。期间有 3 个任务审查发现问题，子代理自动修复并重新审查。

**周四下午** - *完成*

28 个任务全部完成。Claude 生成了总结报告：
- 总耗时：4 小时 20 分钟
- 代码行数：2,400 行
- 测试覆盖率：94%
- 提交次数：56 次

小李运行了测试套件，全部通过。他合并了分支，功能上线。

**反思**："以前我自己做这个功能估计要 3-4 天，现在用 Superpowers，我只花了 1 小时参与问答和确认，AI 做了 4 小时高质量工作。关键是代码质量比我自己写的还好，测试覆盖全面。"

---

### 故事二：技术 Lead 小王的标准化推广

**背景**：小王是 10 人技术团队的 Lead，团队成员都在使用 AI 编程助手，但输出质量参差不齐。

**问题**：
- 有的成员用 AI 生成的代码没有测试
- 有的成员代码风格不统一
- 有的成员在复杂任务上容易走偏
- Code Review 时发现大量基础问题

**解决方案**：小王在团队推广 Superpowers。

**实施过程**：

1. **第1周**：小王自己试用 Superpowers 完成 2 个功能，验证效果
2. **第2周**：在团队会议演示完整工作流，展示代码质量差异
3. **第3周**：团队成员逐个安装，小王提供安装支持
4. **第4周**：制定团队规范：
   - 所有新功能必须使用 brainstorming → writing-plans → subagent-driven-dev 流程
   - Bug 修复必须使用 systematic-debugging
   - 代码审查使用 requesting-code-review 技能

**效果**：

| 指标 | 推广前 | 推广后（3个月） |
|------|--------|----------------|
| 测试覆盖率 | 45% | 89% |
| Code Review 往返次数 | 平均 3.2 次 | 平均 1.1 次 |
| Bug 回归率 | 28% | 7% |
| 功能开发周期 | 2 周 | 1 周 |
| 重构频率 | 每月 2 次 | 每季度 1 次 |

**小王反馈**："Superpowers 最大的价值不是让 AI 写得更快，而是让 AI 写得更规范。它像是一个严格执行代码规范的资深工程师，盯着 AI 的每一步工作。"
