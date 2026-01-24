[根目录](../../../CLAUDE.md) > [python](../../) > [valuecell](../) > **server**

# 服务器模块 (Server)

## 模块职责

服务器模块是基于 FastAPI 的后端服务，为 ValueCell 提供 RESTful API、WebSocket 支持和数据持久化。它负责处理前端请求、管理数据库连接、路由到核心引擎，并提供用户认证、策略管理等业务功能。

## 入口与启动

### 主入口
- **启动文件**: `main.py`
- **启动命令**: `uvicorn valuecell.server.main:app --reload`
- **默认端口**: 8000

### 应用创建
```python
from valuecell.server.api.app import create_app
app = create_app()
```

### 控制机制
- 支持 stdin 控制命令进行优雅关闭
- 线程化控制循环处理
- IDE 调试模式检测

## 对外接口

### API 路由结构

#### 1. 系统相关
- `routers/system.py`: 系统信息和健康检查
- `routers/i18n.py`: 国际化支持

#### 2. 用户管理
- `routers/user_profile.py`: 用户配置文件管理
- `schemas/user_profile.py`: 用户配置数据模型

#### 3. 智能体相关
- `routers/agent.py`: 智能体管理
- `routers/agent_stream.py`: 智能体流式响应
- `schemas/agent.py`: 智能体数据模型

#### 4. 策略交易
- `routers/strategy.py`: 策略管理
- `routers/strategy_api.py`: 策略 API
- `routers/strategy_agent.py`: 策略智能体
- `routers/strategy_prompts.py`: 策略提示词
- `schemas/strategy.py`: 策略数据模型
- `schemas/trading.py`: 交易数据模型

#### 5. 任务管理
- `routers/task.py`: 任务管理
- `schemas/task.py`: 任务数据模型

#### 6. 数据管理
- `routers/watchlist.py`: 观察列表
- `routers/models.py`: 模型管理（v0.1.20 新增 Ollama 支持）
- `routers/conversation.py`: 对话管理
- `schemas/watchlist.py`: 观察列表模型
- `schemas/conversation.py`: 对话模型
- `schemas/model.py`: 模型数据模型

### WebSocket 支持
- 智能体流式响应
- 实时数据推送
- 任务状态更新

## 关键依赖与配置

### 技术栈
- **FastAPI**: Web 框架
- **SQLAlchemy**: ORM
- **aiosqlite**: 异步 SQLite 驱动
- **Pydantic**: 数据验证
- **uvicorn**: ASGI 服务器
- **loguru**: 日志管理

### 配置管理
- `config/settings.py`: 应用设置
- `config/i18n.py`: 国际化配置
- 支持环境变量配置

### 依赖注入
```python
from valuecell.server.api.app import create_app
app = create_app()
```

## 数据模型

### 数据库模型 (db/models/)
- `base.py`: 基础模型类
- `agent.py`: 智能体模型
- `strategy.py`: 策略模型
- `strategy_detail.py`: 策略详情
- `strategy_portfolio.py`: 策略组合
- `strategy_holding.py`: 策略持仓
- `user_profile.py`: 用户配置
- `asset.py`: 资产模型
- `watchlist.py`: 观察列表
- `conversation.py`: 对话模型

### API 模式 (api/schemas/)
- Pydantic 模型定义
- 请求/响应验证
- 数据序列化

## 服务层 (services/)

### 核心服务
- `agent_service.py`: 智能体业务逻辑
- `strategy_service.py`: 策略管理服务
- `conversation_service.py`: 对话服务
- `user_profile_service.py`: 用户服务
- `task_service.py`: 任务服务
- `i18n_service.py`: 国际化服务
- `assets/asset_service.py`: 资产服务

### 数据访问层 (db/repositories/)
- `strategy_repository.py`: 策略数据访问
- `user_profile_repository.py`: 用户数据访问
- `asset_repository.py`: 资产数据访问
- `watchlist_repository.py`: 观察列表数据访问

## 测试与质量

### 测试策略
- 使用 pytest 进行单元测试
- 测试覆盖率要求
- 异步测试支持

### 代码质量
- 遵循 RESTful API 设计原则
- 完整的错误处理
- 请求验证和响应标准化

## 部署与运维

### 环境变量
- `API_HOST`: 服务器地址
- `API_PORT`: 服务器端口
- `API_DEBUG`: 调试模式
- 数据库连接配置

### 日志配置
- 结构化日志输出
- 不同级别的日志记录
- 日志轮转和归档

### 性能优化
- 异步数据库操作
- 连接池管理
- 响应缓存策略

## 常见问题 (FAQ)

### Q: 如何添加新的 API 端点？
A: 在 `routers/` 目录下创建新路由模块，在 `app.py` 中注册。

### Q: 如何配置数据库？
A: 修改 `config/settings.py` 中的数据库配置，使用 SQLAlchemy 连接字符串。

### Q: 如何处理跨域请求？
A: FastAPI 自带 CORS 中间件，在 `app.py` 中配置。

## 相关文件清单

### 应用入口
- `main.py`: 服务器启动入口
- `api/app.py`: FastAPI 应用工厂

### 路由定义
- `api/routers/`: 所有路由模块
- `api/exceptions.py`: 异常处理器

### 配置管理
- `config/settings.py`: 应用配置
- `config/i18n.py`: 国际化配置

### 数据库
- `db/connection.py`: 数据库连接
- `db/init_db.py`: 数据库初始化
- `db/models/`: ORM 模型定义
- `db/repositories/`: 数据访问层

### 业务服务
- `services/`: 业务逻辑服务
- `services/assets/`: 资产相关服务

### API 模式
- `api/schemas/`: Pydantic 模型定义

## 变更记录 (Changelog)
- 2026-01-10: 更新到 v0.1.20，新增 Ollama 模型支持、LLM 等待时间配置、i18n 扩展
- 2025-12-05: 初始文档创建，梳理服务器模块结构