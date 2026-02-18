# Mermaid 图表使用指南

## 图表类型速查

| 场景 | 推荐图类型 | 关键词 |
|------|-----------|--------|
| 功能流程、决策判断 | `flowchart TD` / `flowchart LR` | if/else、步骤、决策 |
| 前后端通信、时序 | `sequenceDiagram` | 请求、响应、调用链 |
| 数据库表关系 | `erDiagram` | 表、字段、外键 |
| 对象结构、继承 | `classDiagram` | 类、属性、方法 |
| 订单/任务状态流转 | `stateDiagram-v2` | 状态、转换、触发 |
| 用户体验旅程 | `journey` | 用户感受、满意度 |
| 模块/概念层级 | `mindmap` | 知识树、功能树 |
| 模块依赖、系统架构 | `graph LR` | 依赖、调用、组件 |

---

## 1. flowchart — 流程图

**适合**：业务流程、功能逻辑、决策树

```mermaid
flowchart TD
    A([用户打开 App]) --> B{是否已登录？}
    B -- 是 --> C[进入首页]
    B -- 否 --> D[跳转登录页]
    D --> E{选择登录方式}
    E -- 手机号 --> F[输入验证码]
    E -- 微信 --> G[微信授权]
    F --> H[验证成功]
    G --> H
    H --> C
    C --> I[浏览内容]
    I --> J{是否付费内容？}
    J -- 免费 --> K[直接查看]
    J -- 付费 --> L[提示开通会员]
    L --> M{用户决定}
    M -- 开通 --> N[支付流程]
    M -- 取消 --> I
    N --> O[解锁内容]
    O --> K

    style A fill:#4CAF50,color:#fff
    style H fill:#2196F3,color:#fff
    style O fill:#FF9800,color:#fff
```

**写作规范**：
- 开始/结束节点用 `([文字])` 圆角矩形
- 决策节点用 `{文字}` 菱形
- 普通步骤用 `[文字]` 方形
- 重要节点加颜色 `style 节点ID fill:#颜色`
- 箭头标签用 `-- 说明文字 -->`

---

## 2. sequenceDiagram — 时序图

**适合**：前后端交互、API 调用、用户操作链路

```mermaid
sequenceDiagram
    actor 用户
    participant 前端APP
    participant 后端API
    participant 数据库
    participant 第三方支付

    用户 ->> 前端APP: 点击「立即购买」
    前端APP ->> 后端API: POST /orders (商品ID, 数量)
    后端API ->> 数据库: 检查库存
    数据库 -->> 后端API: 库存充足
    后端API ->> 数据库: 创建待支付订单
    数据库 -->> 后端API: 订单ID: #1234
    后端API -->> 前端APP: 返回订单信息 + 支付参数
    前端APP ->> 第三方支付: 拉起支付界面
    用户 ->> 第三方支付: 确认支付
    第三方支付 -->> 后端API: 支付回调通知
    后端API ->> 数据库: 更新订单状态为「已付款」
    后端API ->> 数据库: 扣减库存
    后端API -->> 前端APP: 推送支付成功通知
    前端APP ->> 用户: 显示「支付成功」页面

    Note over 后端API,数据库: 数据库操作在事务中执行
    Note over 第三方支付,后端API: 支付回调需验证签名
```

**写作规范**：
- `->>` 表示实线请求，`-->>` 表示虚线响应
- `actor` 表示真实用户，`participant` 表示系统组件
- `Note over A,B:` 添加说明注释
- `loop` / `alt` / `opt` 表示循环/条件/可选分支

---

## 3. erDiagram — 数据库关系图

**适合**：数据模型、表结构、实体关系

```mermaid
erDiagram
    用户表 {
        int id PK "主键，自增"
        string 手机号 UK "唯一，加密存储"
        string 昵称
        string 头像URL
        enum 状态 "正常/禁用/注销"
        datetime 注册时间
        datetime 最后登录时间
    }

    订单表 {
        int id PK
        int 用户ID FK
        string 订单编号 UK
        decimal 实付金额
        enum 支付状态 "待支付/已支付/已退款"
        enum 订单状态 "待发货/已发货/已完成/已取消"
        datetime 创建时间
    }

    订单商品表 {
        int id PK
        int 订单ID FK
        int 商品ID FK
        int 数量
        decimal 单价
    }

    商品表 {
        int id PK
        string 名称
        decimal 原价
        decimal 现价
        int 库存数量
        int 销量
        enum 状态 "上架/下架"
    }

    用户表 ||--o{ 订单表 : "一个用户有多个订单"
    订单表 ||--|{ 订单商品表 : "一个订单含多条商品"
    商品表 ||--o{ 订单商品表 : "一个商品出现在多个订单"
```

**写作规范**：
- `PK` 主键，`FK` 外键，`UK` 唯一键
- 关系符号：`||--||` 一对一，`||--o{` 一对多，`}o--o{` 多对多
- 字段后加引号写业务说明

---

## 4. stateDiagram-v2 — 状态机图

**适合**：订单状态、用户状态、审批流程、任务状态

```mermaid
stateDiagram-v2
    [*] --> 草稿 : 用户创建文章

    草稿 --> 待审核 : 提交审核
    草稿 --> 已删除 : 直接删除

    待审核 --> 已发布 : 审核通过
    待审核 --> 草稿 : 审核不通过（退回修改）
    待审核 --> 已拒绝 : 违规内容

    已发布 --> 已下架 : 管理员下架 / 作者撤回
    已发布 --> 已删除 : 彻底删除

    已下架 --> 已发布 : 重新上架
    已下架 --> 草稿 : 重新编辑
    已下架 --> 已删除 : 彻底删除

    已拒绝 --> 草稿 : 修改后重新提交

    已删除 --> [*]

    note right of 待审核 : 审核时间 SLA：24小时内
    note right of 已发布 : 自动推送给关注用户
```

**写作规范**：
- `[*]` 表示开始和结束
- 箭头上写触发条件（事件）
- `note right of 状态名` 添加补充说明

---

## 5. journey — 用户旅程图

**适合**：用户体验地图、用户满意度分析

```mermaid
journey
    title 新用户从注册到第一次下单的完整旅程
    section 发现产品
        朋友分享链接: 5: 用户
        打开落地页: 4: 用户
        查看功能介绍: 3: 用户
    section 注册登录
        点击「立即注册」: 4: 用户
        填写手机号: 3: 用户
        等待验证码: 2: 用户
        输入验证码: 3: 用户
        完善个人信息: 2: 用户
    section 探索产品
        浏览首页推荐: 4: 用户
        搜索心仪商品: 4: 用户
        查看商品详情: 5: 用户
        加入购物车: 5: 用户
    section 下单支付
        进入购物车: 4: 用户
        选择收货地址: 3: 用户
        选择支付方式: 3: 用户
        确认支付: 4: 用户
        等待付款结果: 2: 用户
        看到「支付成功」: 5: 用户
```

**写作规范**：
- 分数 1-5，代表用户情绪（1=沮丧，5=开心）
- `section` 表示阶段，每个阶段包含多个步骤
- 最后一列写角色名称

---

## 6. mindmap — 思维导图

**适合**：功能全景图、技术栈概览、概念梳理

```mermaid
mindmap
  root((电商平台))
    用户体系
      注册登录
        手机号登录
        第三方登录
      个人中心
        地址管理
        收藏夹
        消息通知
      会员体系
        普通会员
        VIP会员
        积分体系
    商品体系
      商品管理
        上架/下架
        库存管理
        价格体系
      分类体系
        一级分类
        二级分类
      搜索推荐
        关键词搜索
        个性化推荐
    交易体系
      购物车
      订单管理
        下单流程
        订单状态
        退款退货
      支付体系
        微信支付
        支付宝
        余额支付
    运营体系
      促销活动
        优惠券
        满减活动
        限时秒杀
      内容营销
        商品评价
        晒单分享
```

---

## 7. graph — 架构依赖图

**适合**：模块依赖、系统架构、技术组件关系

```mermaid
graph TB
    subgraph 客户端层
        Web[Web 浏览器]
        iOS[iOS App]
        Android[Android App]
        MiniApp[微信小程序]
    end

    subgraph 网关层
        Gateway[API 网关<br/>Nginx + Kong]
        CDN[CDN 加速<br/>静态资源]
    end

    subgraph 业务服务层
        UserSvc[用户服务]
        OrderSvc[订单服务]
        ProductSvc[商品服务]
        PaySvc[支付服务]
        NotifySvc[通知服务]
    end

    subgraph 数据层
        MySQL[(MySQL<br/>主业务数据)]
        Redis[(Redis<br/>缓存+Session)]
        ES[(ElasticSearch<br/>搜索引擎)]
        OSS[(对象存储<br/>图片/文件)]
    end

    subgraph 第三方
        WxPay[微信支付]
        Alipay[支付宝]
        SMS[短信服务]
    end

    Web & iOS & Android & MiniApp --> Gateway
    Gateway --> UserSvc & OrderSvc & ProductSvc & PaySvc
    OrderSvc --> PaySvc
    PaySvc --> NotifySvc
    UserSvc & OrderSvc & ProductSvc --> MySQL
    UserSvc --> Redis
    ProductSvc --> ES
    ProductSvc --> OSS
    PaySvc --> WxPay & Alipay
    NotifySvc --> SMS

    style Gateway fill:#FF5722,color:#fff
    style MySQL fill:#2196F3,color:#fff
    style Redis fill:#F44336,color:#fff
```

---

## 通用写作技巧

### 颜色规范
```
重要节点/开始：fill:#4CAF50,color:#fff（绿色）
关键决策：fill:#FF9800,color:#fff（橙色）
用户操作：fill:#2196F3,color:#fff（蓝色）
错误/警告：fill:#F44336,color:#fff（红色）
第三方服务：fill:#9C27B0,color:#fff（紫色）
```

### 中文友好写法
- 节点 ID 用英文（`A`, `userLogin`, `orderCreate`）
- 显示文字用中文（`[用户点击登录]`）
- 避免节点 ID 包含特殊字符

### 大图优化
- 超过 20 个节点时，考虑拆分成多个子图
- 用 `subgraph` 分组相关节点
- 关键路径用粗线：`A ==> B`（双箭头）
