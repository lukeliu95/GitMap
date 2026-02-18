# 接口与集成图

## API 接口总览

```mermaid
flowchart TB
    subgraph Auth["认证接口 /api/auth"]
        A1[POST /register<br/>用户注册]
        A2[POST /login<br/>用户登录]
        A3[GET /me<br/>获取当前用户]
    end
    
    subgraph Receipts["小票接口 /api/receipts"]
        B1[GET /<br/>获取列表]
        B2[GET /:id/image<br/>获取图片]
        B3[POST /upload<br/>上传图片]
        B4[POST /:id/process<br/>处理识别]
        B5[POST /<br/>保存小票]
        B6[DELETE /:id<br/>删除单张]
        B7[POST /batch-delete<br/>批量删除]
    end
    
    subgraph Reports["报表接口 /api/reports"]
        C1[GET /:period<br/>获取报表]
        C2[POST /:period<br/>保存报表]
    end
    
    subgraph 统一响应格式
        D1["{ success: true, data: ... }"]
        D2["{ error: '错误信息' }"]
    end
    
    Auth --> D1
    Receipts --> D1
    Reports --> D1
```

> **图注**：系统共提供12个REST API接口，分为认证、小票、报表三大类。除了注册和登录，其他接口都需要在 Header 中携带 `Authorization: Bearer {token}` 进行身份验证。

---

## 认证授权流程

```mermaid
sequenceDiagram
    actor U as 用户
    participant F as App前端
    participant A as Auth API
    participant DB as 数据库
    
    U->>F: 输入邮箱和密码
    F->>A: POST /api/auth/login<br/>{email, password}
    
    A->>DB: 查询用户
    DB-->>A: 返回用户记录
    
    alt 用户不存在
        A-->>F: 401 {error: 'User not found'}
        F->>U: 提示用户不存在
    else 密码错误
        A->>A: 对比密码哈希
        A-->>F: 401 {error: 'Invalid password'}
        F->>U: 提示密码错误
    else 验证成功
        A->>A: 生成JWT Token
        A-->>F: 200 {user, token}
        F->>F: 保存token到localStorage
        F->>U: 进入App首页
    end
    
    Note over F,A: 后续请求携带Token
    
    F->>A: GET /api/receipts<br/>Header: Bearer {token}
    A->>A: 验证JWT
    
    alt Token无效
        A-->>F: 401 {error: 'Invalid token'}
        F->>F: 清除token，跳转到登录
    else Token有效
        A->>A: 提取userId
        A->>DB: 查询该用户数据
        DB-->>A: 返回数据
        A-->>F: 200 {receipts: [...]}
    end
```

> **图注**：认证采用 JWT（JSON Web Token）方案。登录成功后服务端返回 Token，前端保存在 localStorage。后续所有请求在 Header 中携带 Token，服务端验证通过后提取 userId 进行数据查询。Token 失效时前端自动跳转登录页。

---

## 典型 API 调用时序

### 1. 单张小票识别完整流程

```mermaid
sequenceDiagram
    actor U as 用户
    participant F as App前端
    participant API as 后端API
    participant AI as Gemini AI
    participant DB as 数据库
    
    U->>F: 拍摄小票照片
    F->>F: 1. 压缩图片
    F->>API: 2. POST /api/receipts/upload<br/>{imageData: base64}
    
    API->>API: 3. 生成receipt ID
    API->>DB: 4. INSERT receipts<br/>status: 'pending'
    API-->>F: 5. {id, status: 'pending'}
    
    F->>F: 6. 显示"正在分析"动画
    F->>API: 7. POST /api/receipts/:id/process<br/>{timezone}
    
    API->>DB: 8. SELECT imageUrl
    DB-->>API: 9. 返回图片数据
    
    API->>AI: 10. 调用Gemini OCR<br/>识别小票内容
    AI-->>API: 11. {storeName, date, total, items}
    
    API->>DB: 12. SELECT * FROM receipts<br/>WHERE date = ? AND status='completed'
    
    alt 存在重复
        DB-->>API: 13. 返回已有记录
        API->>DB: 14. DELETE pending receipt
        API-->>F: 15. {isDuplicate: true}
        F->>U: 16. 提示"该小票已存在"
    else 无重复
        DB-->>API: 13. 无记录
        API->>DB: 14. SELECT 近3天消费记录
        DB-->>API: 15. 返回历史数据
        
        API->>AI: 16. 生成消费分析
        AI-->>API: 17. 分析文本
        
        API->>DB: 18. UPDATE receipt<br/>SET status='completed'<br/>INSERT items
        API-->>F: 19. {receipt: {...}}
        F->>F: 20. 更新列表状态
        F->>U: 21. 显示结果+分析弹窗
    end
```

> **图注**：单张小票识别涉及 21 个步骤，前后端通信 3 次，AI 调用 2 次。关键节点包括：上传图片获取ID、AI识别内容、重复检测、对比历史生成分析。整个流程约需 3-5 秒。

### 2. 批量处理流程

```mermaid
sequenceDiagram
    actor U as 用户
    participant F as App前端
    participant API as 后端API
    participant AI as Gemini AI
    participant DB as 数据库
    
    U->>F: 选择6张小票
    F->>F: 1. 批量压缩
    
    par 并行上传
        F->>API: upload #1
        F->>API: upload #2
        F->>API: upload #3
        F->>API: upload #4
        F->>API: upload #5
        F->>API: upload #6
    end
    
    API-->>F: 2. 返回6个ID
    F->>F: 3. 显示批量进度 0/6
    
    loop 逐个处理
        F->>API: 4. process #1
        API->>AI: 5. OCR识别
        AI-->>API: 6. 结果
        API->>DB: 7. 保存结果
        API-->>F: 8. 完成#1
        F->>F: 9. 更新进度 1/6
    end
    
    F->>U: 10. 全部完成提示
```

> **图注**：批量处理采用"并行上传 + 串行处理"策略。上传阶段并行可以最大化利用带宽，处理阶段串行避免 AI API 限流。用户通过进度条实时了解处理进度。

### 3. 报表生成流程

```mermaid
sequenceDiagram
    actor U as 用户
    participant F as App前端
    participant API as 后端API
    participant AI as Gemini AI
    participant DB as 数据库
    
    U->>F: 1. 点击"周报"
    F->>API: 2. GET /api/reports/week
    API->>DB: 3. SELECT report<br/>WHERE period='week'
    
    alt 报告存在
        DB-->>API: 4. 返回缓存内容
        API-->>F: 5. {content, updatedAt}
        F->>U: 6. 直接展示
    else 报告不存在
        DB-->>API: 4. 无记录
        API->>DB: 5. SELECT 本周所有小票
        DB-->>API: 6. 返回消费数据
        
        API->>AI: 7. 生成周报<br/>统计数据+明细
        AI-->>API: 8. Markdown报告
        
        API->>DB: 9. INSERT report<br/>period='week'
        API-->>F: 10. {content, updatedAt}
        F->>U: 11. 展示报告
    end
    
    U->>F: 12. 点击"刷新"
    F->>API: 13. 强制重新生成
    API->>API: 跳过缓存查询
    Note over API: 执行5-11步
```

> **图注**：报表采用缓存优先策略。首次生成后保存到数据库，下次直接读取。用户手动刷新时跳过缓存重新生成。这种设计既保证了加载速度，又允许用户获取最新数据。

---

## 第三方服务集成地图

```mermaid
flowchart TB
    subgraph 我们的系统["花在哪里了系统"]
        A1[App前端]
        A2[后端API]
    end
    
    subgraph Google["Google AI"]
        B1[Gemini 3 Flash API]
        B2[OCR识别能力]
        B3[文本生成能力]
    end
    
    subgraph Turso["Turso Database"]
        C1[云端SQLite]
        C2[libSQL协议]
    end
    
    subgraph Vercel["Vercel Platform"]
        D1[CDN加速]
        D2[Serverless Functions]
    end
    
    subgraph 外部服务["外部服务"]
        E1[用户设备相机]
        E2[用户相册]
    end
    
    A1 <-->|1. 拍照/选图| E1
    A1 <-->|2. 选图| E2
    A1 -->|3. API请求| A2
    A2 -->|4. OCR+分析| B1
    B1 -.-> B2
    B1 -.-> B3
    A2 <-->|5. SQL查询| C1
    C1 -.-> C2
    A1 <-->|6. 静态资源| D1
    A2 -->|7. Serverless执行| D2
    
    style B1 fill:#e3f2fd
    style C1 fill:#e8f5e9
    style D1 fill:#fff3e0
```

> **图注**：系统集成3个外部服务：Google Gemini 提供 AI 能力（OCR+文本生成），Turso 提供云端数据库存储，Vercel 提供部署和 CDN 服务。设备相机和相册通过浏览器/系统 API 本地调用。

---

## API 详细规范

### 认证接口

#### POST /api/auth/register

**请求：**
```json
{
  "email": "user@example.com",
  "password": "123456"
}
```

**成功响应 (200)：**
```json
{
  "user": {
    "id": "usr_xxx",
    "email": "user@example.com",
    "createdAt": "2024-01-15T08:30:00Z"
  },
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

**错误响应 (400/500)：**
```json
{
  "error": "Email already registered"
}
```

---

#### POST /api/auth/login

**请求：**
```json
{
  "email": "user@example.com",
  "password": "123456"
}
```

**成功响应 (200)：**
```json
{
  "user": {
    "id": "usr_xxx",
    "email": "user@example.com",
    "createdAt": "2024-01-15T08:30:00Z"
  },
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

---

### 小票接口

#### POST /api/receipts/upload

**请求头：**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**请求体：**
```json
{
  "imageData": "data:image/webp;base64,UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoBAAEADsD+JaQAA3AAAAAA..."
}
```

**成功响应 (200)：**
```json
{
  "id": "rcp_xxx",
  "status": "pending"
}
```

---

#### POST /api/receipts/:id/process

**请求头：**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**请求体：**
```json
{
  "timezone": "Asia/Tokyo"
}
```

**成功响应 - 正常情况 (200)：**
```json
{
  "receipt": {
    "id": "rcp_xxx",
    "storeName": "7-Eleven",
    "date": "2024-01-15",
    "total": 28.5,
    "currency": "¥",
    "items": [
      {
        "id": "itm_xxx",
        "name": "照烧鸡肉饭",
        "price": 25.0,
        "nutrition": "热量: 650kcal"
      }
    ],
    "status": "completed",
    "analysis": "## 消费分析\n\n### 营养健康评估..."
  },
  "isDuplicate": false
}
```

**成功响应 - 重复情况 (200)：**
```json
{
  "receipt": null,
  "isDuplicate": true,
  "duplicateStore": "7-Eleven",
  "duplicateDate": "2024-01-15"
}
```

---

#### GET /api/receipts

**请求头：**
```
Authorization: Bearer {token}
```

**成功响应 (200)：**
```json
[
  {
    "id": "rcp_xxx",
    "storeName": "7-Eleven",
    "date": "2024-01-15",
    "total": 28.5,
    "currency": "¥",
    "status": "completed",
    "items": [...],
    "analysis": "..."
  }
]
```

---

### 报表接口

#### GET /api/reports/:period

**路径参数：**
- `period`: `week` | `month` | `all`

**请求头：**
```
Authorization: Bearer {token}
```

**成功响应 - 有缓存 (200)：**
```json
{
  "content": "# 本周消费报告...",
  "updatedAt": "2024-01-21T18:00:00Z"
}
```

**成功响应 - 无缓存 (200)：**
```json
{
  "content": null,
  "updatedAt": null
}
```

---

#### POST /api/reports/:period

**路径参数：**
- `period`: `week` | `month` | `all`

**请求头：**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**请求体：**
```json
{
  "content": "# 本周消费报告\n\n## 消费概览..."
}
```

**成功响应 (200)：**
```json
{
  "success": true,
  "updatedAt": "2024-01-21T18:00:00Z"
}
```

---

## 错误处理规范

```mermaid
flowchart TB
    A[API错误类型] --> B[400 Bad Request]
    A --> C[401 Unauthorized]
    A --> D[403 Forbidden]
    A --> E[404 Not Found]
    A --> F[500 Internal Error]
    
    B --> B1[参数缺失<br/>格式错误]
    C --> C1[Token缺失<br/>Token无效<br/>Token过期]
    D --> D1[无权访问<br/>非自己的数据]
    E --> E1[资源不存在]
    F --> F1[服务器错误<br/>AI服务错误<br/>数据库错误]
    
    subgraph 前端处理
        G1[显示友好提示]
        G2[401时跳转登录]
        G3[500时重试]
    end
    
    C --> G2
    B --> G1
    D --> G1
    E --> G1
    F --> G3
```

> **图注**：错误处理遵循 HTTP 状态码规范。401 专门处理认证问题，触发前端跳转登录页；400/403/404 显示具体错误信息；500 提供重试机制。所有错误响应都包含 `error` 字段说明原因。
