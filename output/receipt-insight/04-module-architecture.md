# 模块架构图

## 系统整体架构

```mermaid
graph TB
    subgraph 客户端层["客户端层 (Client Layer)"]
        C1[React 19 应用]
        C2[Vite 构建工具]
        C3[TailwindCSS 样式]
    end
    
    subgraph 状态管理层["状态管理层 (State Layer)"]
        S1[Zustand Store]
        S2[useAuthStore<br/>用户状态]
        S3[useReceiptStore<br/>小票状态]
    end
    
    subgraph 服务层["服务层 (Service Layer)"]
        V1[API服务<br/>services/api.ts]
        V2[Gemini服务<br/>services/gemini.ts]
    end
    
    subgraph 组件层["组件层 (Component Layer)"]
        UI1[MagicScan<br/>扫描动画]
        UI2[ReceiptList<br/>小票列表]
        UI3[ReceiptDetail<br/>小票详情]
        UI4[ReceiptAnalysis<br/>分析弹窗]
        UI5[WeeklyReportView<br/>统计报表]
        UI6[LoginPage<br/>登录页]
    end
    
    subgraph 后端层["后端层 (Backend Layer)"]
        B1[Express 服务器]
        B2[Auth 路由<br/>注册/登录]
        B3[Receipt 路由<br/>CRUD]
        B4[Report 路由<br/>报表]
    end
    
    subgraph 数据处理层["数据处理层 (Data Layer)"]
        P1[db.js<br/>数据库连接]
        P2[auth.js<br/>认证逻辑]
        P3[gemini.js<br/>AI处理]
    end
    
    subgraph 存储层["存储层 (Storage Layer)"]
        D1[SQLite/libSQL<br/>本地开发]
        D2[Turso<br/>云端生产]
    end
    
    subgraph AI层["AI层 (AI Layer)"]
        AI1[Google Gemini API]
        AI2[OCR 识别]
        AI3[文本分析]
        AI4[报告生成]
    end
    
    C1 --> S1
    S1 --> V1
    V1 --> B1
    C1 --> UI1
    C1 --> UI2
    C1 --> UI3
    C1 --> UI4
    C1 --> UI5
    C1 --> UI6
    
    B1 --> B2
    B1 --> B3
    B1 --> B4
    
    B2 --> P2
    B3 --> P1
    B3 --> P3
    B4 --> P1
    
    P1 --> D1
    P1 --> D2
    P3 --> AI1
    
    AI1 --> AI2
    AI1 --> AI3
    AI1 --> AI4
```

> **图注**：系统采用经典的分层架构。前端使用 React + Zustand 管理状态，通过服务层与后端通信。后端使用 Express 提供 REST API，数据处理层封装数据库和 AI 调用逻辑。存储层根据环境自动切换，AI 层统一调用 Google Gemini。

---

## 前端模块架构

```mermaid
flowchart TB
    subgraph App["App.tsx 主入口"]
        A1[路由状态管理]
        A2[全局UI状态]
    end
    
    subgraph Stores["Zustand Stores"]
        B1[useAuthStore]
        B2[useReceiptStore]
    end
    
    subgraph Components["组件库"]
        C1[MagicScan]
        C2[ReceiptList]
        C3[ReceiptDetail]
        C4[ReceiptAnalysis]
        C5[WeeklyReportView]
        C6[BatchProgress]
        C7[OnboardingView]
        C8[LoginPage]
    end
    
    subgraph Services["服务层"]
        D1[api.ts<br/>后端API封装]
        D2[gemini.ts<br/>前端AI调用]
    end
    
    subgraph Utils["工具库"]
        E1[utils.ts<br/>通用函数]
        E2[types.ts<br/>类型定义]
    end
    
    A1 --> B1
    A1 --> B2
    A2 --> C1
    A2 --> C2
    
    B1 --> C8
    B2 --> C2
    B2 --> C3
    
    C1 --> D1
    C2 --> D1
    C3 --> D1
    C4 --> D1
    C5 --> D1
    C5 --> D2
    
    D1 --> D2
    
    C1 -.-> E2
    C2 -.-> E2
    B1 -.-> E2
    B2 -.-> E2
```

> **图注**：前端采用组件化架构，按功能拆分组件。状态管理使用 Zustand，分为用户状态和小票状态两个 Store。服务层封装所有 API 调用，组件通过服务层与后端通信。工具库提供类型定义和通用函数。

---

## 后端模块架构

```mermaid
flowchart TB
    subgraph Server["server/index.js 主入口"]
        A1[Express App]
        A2[中间件注册]
        A3[路由注册]
    end
    
    subgraph Routes["API 路由"]
        B1[/api/auth/*<br/>认证路由]
        B2[/api/receipts/*<br/>小票路由]
        B3[/api/reports/*<br/>报表路由]
    end
    
    subgraph Middleware["中间件"]
        C1[CORS 跨域]
        C2[JSON 解析]
        C3[DB 初始化]
        C4[Auth 验证]
    end
    
    subgraph Modules["功能模块"]
        D1[auth.js<br/>密码+JWT]
        D2[db.js<br/>数据库连接]
        D3[gemini.js<br/>AI封装]
    end
    
    subgraph Database["数据库"]
        E1[SQLite 本地]
        E2[Turso 云端]
    end
    
    A1 --> A2
    A1 --> A3
    
    A2 --> C1
    A2 --> C2
    A2 --> C3
    
    A3 --> B1
    A3 --> B2
    A3 --> B3
    
    B1 --> C4
    B2 --> C4
    B3 --> C4
    
    B1 --> D1
    B2 --> D2
    B2 --> D3
    B3 --> D2
    
    D2 --> E1
    D2 --> E2
    
    D3 --> AI[Google Gemini API]
```

> **图注**：后端采用 Express 框架，按路由、中间件、功能模块分层。认证路由处理注册登录，小票路由处理核心业务流程，报表路由处理统计功能。中间件统一处理跨域、解析、初始化和认证。功能模块封装具体业务逻辑。

---

## 请求处理链路

```mermaid
flowchart LR
    A[HTTP请求] --> B{路由匹配}
    
    B -->|/api/auth/*| C[认证路由]
    B -->|/api/receipts/*| D[小票路由]
    B -->|/api/reports/*| E[报表路由]
    
    C --> F[中间件链]
    D --> F
    E --> F
    
    F --> G[CORS]
    G --> H[JSON解析]
    H --> I[DB初始化]
    I --> J{需要认证?}
    
    J -->|是| K[Auth验证]
    J -->|否| L[处理请求]
    K -->|验证通过| L
    K -->|验证失败| M[返回401]
    
    L --> N[调用数据库]
    N --> O[调用AI服务]
    O --> P[构建响应]
    P --> Q[返回JSON]
    
    style Q fill:#c8e6c9
    style M fill:#ffcdd2
```

> **图注**：请求处理遵循 Express 标准的中间件模式。所有请求先经过 CORS、JSON解析、DB初始化，然后根据路由决定是否需要认证，最后执行业务逻辑。错误处理在中间件链中统一捕获。

---

## 技术栈全景图

```mermaid
mindmap
  root((技术栈全景))
    前端技术
      React 19
        Hooks
        Context
      TypeScript
        类型安全
      Vite
        快速构建
        HMR热更新
      TailwindCSS v4
        原子化CSS
        响应式设计
      Framer Motion
        页面动画
        手势交互
      Zustand
        轻量状态管理
        无需Provider
    
    后端技术
      Node.js
        运行时
      Express 5
        Web框架
        中间件机制
      JWT
        身份认证
        无状态登录
      bcryptjs
        密码加密
    
    数据库
      libSQL
        SQLite兼容
        异步API
      Turso
        云端SQLite
        Serverless
    
    AI能力
      Google Gemini
        Gemini 3 Flash
        OCR识别
        文本生成
    
    部署
      Vercel
        Serverless
        CDN加速
        自动部署
```

> **图注**：技术栈选择遵循"现代化、轻量级、高效能"原则。前端使用 React 19 + TypeScript，构建工具用 Vite；后端使用 Express 5；数据库采用 libSQL/SQLite 保持轻量；AI 使用 Google Gemini；部署在 Vercel 实现 Serverless 架构。

---

## 模块依赖关系

```mermaid
flowchart TB
    subgraph Frontend["前端依赖"]
        F1[App.tsx]
        F2[Components]
        F3[Stores]
        F4[Services]
    end
    
    subgraph Backend["后端依赖"]
        B1[index.js]
        B2[auth.js]
        B3[db.js]
        B4[gemini.js]
    end
    
    subgraph External["外部依赖"]
        E1[Google Gemini API]
        E2[Turso/SQLite]
    end
    
    F1 --> F2
    F1 --> F3
    F2 --> F3
    F3 --> F4
    
    B1 --> B2
    B1 --> B3
    B1 --> B4
    B2 --> B3
    B4 --> B3
    
    F4 -.HTTP.-> B1
    B4 -.API.-> E1
    B3 -.SQL.-> E2
```

> **图注**：依赖关系遵循单向原则。前端内部：App 依赖组件和状态，组件依赖状态，状态依赖服务。后端内部：入口依赖各模块，auth 和 gemini 都依赖 db。前后端通过 HTTP 通信，后端通过 API 调用外部服务。

---

## 部署架构

```mermaid
flowchart TB
    subgraph Vercel["Vercel 平台"]
        subgraph Edge["Edge Network"]
            CDN[CDN节点<br/>静态资源缓存]
        end
        
        subgraph Functions["Serverless Functions"]
            API[api/index.js<br/>Express App]
        end
    end
    
    subgraph Database["数据库"]
        Turso[(Turso<br/>云端SQLite)]
    end
    
    subgraph AI["AI服务"]
        Gemini[Google Gemini API]
    end
    
    subgraph User["用户"]
        Browser[浏览器]
        Mobile[手机]
    end
    
    Browser -->|请求静态资源| CDN
    Mobile -->|请求静态资源| CDN
    
    Browser -->|API请求| API
    Mobile -->|API请求| API
    
    API -->|SQL查询| Turso
    API -->|AI调用| Gemini
    
    CDN -->|返回<br/>index.html<br/>JS/CSS| Browser
    API -->|返回<br/>JSON数据| Browser
```

> **图注**：部署采用 Vercel 的 Serverless 架构。前端静态资源通过 CDN 全球分发，API 请求路由到 Serverless Function。数据库使用 Turso 云端 SQLite，AI 调用 Google Gemini API。整个架构无服务器运维，自动扩缩容。

---

## 环境配置策略

```mermaid
flowchart LR
    subgraph 开发环境["本地开发"]
        A1[Vite Dev Server]
        A2[Express Server<br/>localhost:3001]
        A3[SQLite 文件<br/>data/receipts.db]
        A4[uploads 目录<br/>本地存储图片]
    end
    
    subgraph 生产环境["Vercel 生产"]
        B1[Vercel CDN]
        B2[Serverless Function]
        B3[Turso 数据库]
        B4[Base64 存数据库]
    end
    
    subgraph 环境变量["环境变量控制"]
        C1[.env 本地配置]
        C2[Vercel 环境变量]
    end
    
    subgraph 自动切换["自动适配逻辑"]
        D1{检查<br/>TURSO_DATABASE_URL}
        D2|存在| 生产模式
        D3|不存在| 开发模式
    end
    
    A1 --> A2
    A2 --> A3
    A2 --> A4
    
    B1 --> B2
    B2 --> B3
    B2 --> B4
    
    C1 --> A2
    C2 --> B2
    
    B2 --> D1
    A2 --> D1
```

> **图注**：环境配置通过环境变量自动切换。开发环境使用本地 SQLite 和文件系统存储图片；生产环境使用 Turso 云端数据库，图片以 Base64 存入数据库。代码层面通过判断 TURSO_DATABASE_URL 是否存在来自动适配。
