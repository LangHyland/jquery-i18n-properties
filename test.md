# Java DDD架构层次关系图

```
                                      前端请求
                                         │
                                         ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                               表示层 (Presentation Layer)                    │
│                                                                             │
│  ┌─────────────────┐                                                        │
│  │    Controller   │ 接收用户请求，返回响应                                  │
│  │  (表现层适配器)  │ @RestController/@Controller                            │
│  └────────┬────────┘                                                        │
└───────────┼─────────────────────────────────────────────────────────────────┘
            │ 调用应用服务
            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                               应用层 (Application Layer)                     │
│                                                                             │
│  ┌─────────────────┐    ┌─────────────────┐     ┌─────────────────┐         │
│  │   Application   │    │     Command     │     │      Query      │         │
│  │     Service     │    │     Handler     │     │     Handler     │         │
│  │   (应用服务)     │    │   (命令处理器)  │     │   (查询处理器)  │         │
│  └────────┬────────┘    └────────┬────────┘     └────────┬────────┘         │
└───────────┼─────────────────────┼─────────────────────────────────────────┬─┘
            │                     │                                         │
            │ 协调领域对象          │ 处理命令                                │ 执行查询
            ▼                     ▼                                         ▼
┌─────────────────────────────────────────────────────────────────────┐  ┌──────────────────┐
│                         领域层 (Domain Layer)                         │  │     查询层       │
│                                                                      │  │  (Query Layer)   │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐   │  │                  │
│  │     Domain      │    │     Domain      │    │     Domain      │   │  │ ┌──────────────┐ │
│  │     Service     │    │     Entity      │    │  Value Object   │   │  │ │    Query     │ │
│  │   (领域服务)     │    │   (实体对象)    │    │  (值对象)        │   │  │ │   Object    │ │
│  └────────┬────────┘    └────────┬────────┘    └─────────────────┘   │  │ │ (查询对象)   │ │
│           │                      │                                    │  │ └──────────────┘ │
│           └──────────────────────┘                                    │  │                  │
└───────────────────────────────────┬────────────────────────────────────┘  └────────┬────────┘
                                    │ 执行持久化                                     │
                                    ▼                                               │
┌───────────────────────────────────────────────────────────────────────────────────┴─────────┐
│                                    基础设施层 (Infrastructure Layer)                          │
│                                                                                               │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐     │
│  │   Repository    │    │     Mapper      │    │    Database     │    │  External API   │     │
│  │    Interface    │    │                 │    │   Connection    │    │     Adapter     │     │
│  │  (仓储接口)      │    │  (ORM映射)      │    │  (数据库连接)    │    │  (外部API适配器) │     │
│  └────────┬────────┘    └────────┬────────┘    └────────┬────────┘    └────────┬────────┘     │
│           │                      │                      │                      │              │
│           └──────────────────────┴──────────────────────┴──────────────────────┘              │
└───────────────────────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
                                数据库/外部系统

## 各层职责说明

### 表示层 (Presentation Layer)
- **控制器 (Controller)**: 负责处理HTTP请求，将请求委托给应用层，并将结果返回给客户端
- 不包含业务逻辑，只负责请求解析、参数校验、结果封装等

### 应用层 (Application Layer)
- **应用服务 (Application Service)**: 编排领域对象完成用户用例
- **命令处理器 (Command Handler)**: 处理写操作命令
- **查询处理器 (Query Handler)**: 处理读操作请求
- 不包含核心业务逻辑，只负责协调

### 领域层 (Domain Layer)
- **领域服务 (Domain Service)**: 封装跨实体的复杂业务逻辑
- **实体 (Entity)**: 具有身份标识的领域对象，包含业务规则和状态变更逻辑
- **值对象 (Value Object)**: 无身份标识的不可变对象，用于描述领域概念
- **聚合根 (Aggregate Root)**: 保证业务完整性的实体集合入口
- **领域事件 (Domain Event)**: 描述领域中发生的事件

### 查询层 (Query Layer)
- **查询对象 (Query Object)**: 封装查询逻辑，直接操作数据源进行高效查询
- 通常绕过领域层，采用CQRS模式提高查询性能

### 基础设施层 (Infrastructure Layer)
- **仓储实现 (Repository Implementation)**: 实现领域对象的持久化
- **ORM映射 (Mapper)**: 对象关系映射
- **数据库连接 (Database Connection)**: 与数据库的交互
- **外部服务适配器 (External Service Adapter)**: 与外部系统的集成

## 关键DDD概念

- **限界上下文 (Bounded Context)**: 明确业务边界，构建统一语言
- **通用语言 (Ubiquitous Language)**: 团队共享的业务术语体系
- **聚合 (Aggregate)**: 确保业务不变性的对象集合
- **实体 (Entity)**: 有唯一标识的对象
- **值对象 (Value Object)**: 无标识，通过属性值定义的对象
- **领域事件 (Domain Event)**: 领域中发生的事件
- **仓储 (Repository)**: 提供对聚合的存取 
