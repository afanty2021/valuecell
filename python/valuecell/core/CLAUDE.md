[根目录](../../../CLAUDE.md) > [python](../../) > [valuecell](../) > **core**

# 核心引擎模块 (Core)

## 模块职责

核心引擎是 ValueCell 的大脑，负责协调各个智能体的工作，管理用户请求的整个生命周期。它实现了异步、可重入的编排系统，支持人机协作（HITL）和流式响应。

## 入口与启动

### 核心入口
- **Orchestrator**: `coordinate/orchestrator.py::AgentOrchestrator.process_user_input`
- 主要协调整个请求处理流程

### 关键组件初始化
```python
# 编排器初始化
orchestrator = AgentOrchestrator(
    conversation_service=conversation_service,
    event_service=event_service,
    plan_service=plan_service,
    super_agent_service=super_agent_service,
    task_executor=task_executor
)
```

## 对外接口

### 主要服务接口

#### 1. AgentOrchestrator (coordinate/orchestrator.py)
- `process_user_input(user_input)`: 处理用户输入的主入口
- 支持异步生成器模式，提供流式响应
- 管理执行上下文和会话状态

#### 2. SuperAgentService (super_agent/service.py)
- 请求分类和初步处理
- 决定直接回答或交给规划器
- 可选的查询增强

#### 3. PlanService (plan/service.py)
- 将自然语言转换为执行计划
- 支持人机协作（HITL）请求澄清
- 管理待处理的用户输入

#### 4. TaskExecutor (task/executor.py)
- 执行计划中的具体任务
- 支持定时任务
- 集成 A2A 通信协议

#### 5. EventResponseService (event/service.py)
- 事件路由和响应转换
- 响应缓冲和聚合
- 持久化到对话存储

#### 6. ConversationService (conversation/service.py)
- 对话状态管理
- 消息历史存储
- 会话上下文维护

## 关键依赖与配置

### 内部依赖
- **valuecell.core.types**: 核心类型定义
- **valuecell.core.constants**: 常量定义
- **valuecell.utils**: 工具函数

### 外部依赖
- `loguru`: 日志记录
- `asyncio`: 异步编程支持
- `pydantic`: 数据模型验证

### 配置要点
- DEFAULT_CONTEXT_TIMEOUT_SECONDS: 3600 (1小时)
- ASYNC_SLEEP_INTERVAL: 0.1秒

## 数据模型

### 核心模型
- **ExecutionContext**: 执行上下文，管理中断的执行状态
- **UserInput**: 用户输入模型
- **BaseResponse**: 响应基类
- **ExecutionPlan**: 执行计划模型
- **SuperAgentOutcome**: 超级智能体决策结果

## 测试与质量

### 测试目录结构
- `coordinate/tests/`: 编排器测试
- `super_agent/tests/`: 超级智能体测试
- `plan/tests/`: 规划器测试
- `task/tests/`: 任务执行器测试
- `event/tests/`: 事件处理测试
- `conversation/tests/`: 对话管理测试

### 关键测试用例
- 端到端流程测试
- 异步重入测试
- 人机协作测试
- 错误处理测试

## 常见问题 (FAQ)

### Q: 如何添加新的智能体？
A: 实现 agent/decorator.py 装饰的异步函数，创建能力卡片，注册到系统。

### Q: 如何处理长时间运行的任务？
A: TaskExecutor 支持后台执行，即使客户端断开连接也会继续运行。

### Q: 如何扩展响应类型？
A: 继承 BaseResponse，在 ResponseRouter 中添加路由规则。

## 相关文件清单

### 核心文件
- `coordinate/orchestrator.py`: 主编排器
- `coordinate/services.py`: 服务组合
- `super_agent/core.py`: 超级智能体核心逻辑
- `plan/planner.py`: 规划器实现
- `task/executor.py`: 任务执行器
- `event/router.py`: 事件路由器
- `conversation/manager.py`: 对话管理器

### 模型定义
- `types.py`: 核心类型
- `coordinate/__init__.py`: 编排器类型
- `plan/models.py`: 计划模型
- `task/models.py`: 任务模型
- `conversation/models.py`: 对话模型
- `super_agent/models.py`: 超级智能体模型

### 服务层
- `super_agent/service.py`: 超级智能体服务
- `plan/service.py`: 规划服务
- `task/service.py`: 任务服务
- `event/service.py`: 事件响应服务
- `conversation/service.py`: 对话服务

## 变更记录 (Changelog)
- 2025-12-05: 初始文档创建，梳理核心模块结构