# API参考

<cite>
**本文档中引用的文件**  
- [main.py](file://python/valuecell/server/main.py)
- [app.py](file://python/valuecell/server/api/app.py)
- [agent.py](file://python/valuecell/server/api/routers/agent.py)
- [conversation.py](file://python/valuecell/server/api/routers/conversation.py)
- [task.py](file://python/valuecell/server/api/routers/task.py)
- [strategy_api.py](file://python/valuecell/server/api/routers/strategy_api.py)
- [agent_stream.py](file://python/valuecell/server/api/routers/agent_stream.py)
- [system.py](file://python/valuecell/server/api/routers/system.py)
- [models.py](file://python/valuecell/server/api/routers/models.py)
- [watchlist.py](file://python/valuecell/server/api/routers/watchlist.py)
- [user_profile.py](file://python/valuecell/server/api/routers/user_profile.py)
- [i18n.py](file://python/valuecell/server/api/routers/i18n.py)
- [base.py](file://python/valuecell/server/api/schemas/base.py)
- [agent.py](file://python/valuecell/server/api/schemas/agent.py)
- [conversation.py](file://python/valuecell/server/api/schemas/conversation.py)
- [task.py](file://python/valuecell/server/api/schemas/task.py)
- [model.py](file://python/valuecell/server/api/schemas/model.py)
- [watchlist.py](file://python/valuecell/server/api/schemas/watchlist.py)
- [user_profile.py](file://python/valuecell/server/api/schemas/user_profile.py)
- [i18n.py](file://python/valuecell/server/api/schemas/i18n.py)
</cite>

## 目录
1. [简介](#简介)
2. [API基础](#api基础)
3. [智能体管理API](#智能体管理api)
4. [对话管理API](#对话管理api)
5. [任务管理API](#任务管理api)
6. [策略交易API](#策略交易api)
7. [流式API](#流式api)
8. [系统与配置API](#系统与配置api)
9. [观察列表API](#观察列表api)
10. [用户配置文件API](#用户配置文件api)
11. [国际化API](#国际化api)
12. [错误处理](#错误处理)
13. [客户端实现指南](#客户端实现指南)

## 简介

ValueCell后端提供了一套全面的RESTful API，用于管理智能体、对话、策略交易和任务。API基于FastAPI框架构建，遵循标准的HTTP方法和状态码。所有API端点都返回统一的响应格式，包含状态信息、数据和消息。

API的根路径为`/api/v1`，所有端点都以此为前缀。服务器在启动时会初始化数据库表和数据适配器，支持Yahoo Finance、AKShare和BaoStock等数据源。

**Section sources**
- [main.py](file://python/valuecell/server/main.py#L1-L99)
- [app.py](file://python/valuecell/server/api/app.py#L1-L242)

## API基础

### 认证方法
ValueCell API目前使用基于请求头的简单认证机制。客户端需要在请求头中包含`X-User-ID`来标识用户。对于某些端点，还需要提供`X-Session-ID`。

```http
X-User-ID: default_user
X-Session-ID: session_123456
```

### 响应格式
所有API响应都遵循统一的格式，包含状态码、消息和数据。成功响应使用`200 OK`状态码，错误响应使用相应的HTTP状态码。

```json
{
  "code": 200,
  "msg": "请求成功",
  "data": {
    // 具体数据
  }
}
```

### HTTP方法
- `GET`: 获取资源
- `POST`: 创建资源或执行操作
- `PUT`: 更新资源
- `DELETE`: 删除资源

### 通用错误代码
| 状态码 | 错误类型 | 描述 |
|--------|---------|------|
| 400 | BAD_REQUEST | 请求参数无效 |
| 404 | NOT_FOUND | 资源未找到 |
| 500 | INTERNAL_SERVER_ERROR | 服务器内部错误 |

**Section sources**
- [base.py](file://python/valuecell/server/api/schemas/base.py#L1-L121)
- [app.py](file://python/valuecell/server/api/app.py#L1-L242)

## 智能体管理API

智能体管理API提供了对系统中智能体的完整管理功能，包括查询、启用/禁用等操作。

### 获取所有智能体
获取系统中所有智能体的列表。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/agents`
- **参数**:
  - `enabled_only` (布尔值): 是否只返回启用的智能体
  - `name_filter` (字符串): 按名称过滤智能体（支持模糊匹配）

**响应示例**:
```json
{
  "code": 200,
  "msg": "Successfully retrieved 5 agents",
  "data": {
    "agents": [
      {
        "agent_name": "trading_agent",
        "display_name": "交易智能体",
        "description": "执行股票交易的智能体",
        "enabled": true
      }
    ],
    "total": 1,
    "enabled_count": 1
  }
}
```

### 按ID获取智能体
通过智能体ID获取详细信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/agents/{agent_id}`
- **路径参数**:
  - `agent_id` (整数): 智能体的唯一标识符

### 按名称获取智能体
通过智能体名称获取详细信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/agents/by-name/{agent_name}`
- **路径参数**:
  - `agent_name` (字符串): 智能体的名称

### 更新智能体启用状态
启用或禁用指定名称的智能体。

- **HTTP方法**: `POST`
- **URL**: `/api/v1/agents/{agent_name}/enable`
- **路径参数**:
  - `agent_name` (字符串): 要更新的智能体名称
- **请求体**:
```json
{
  "enabled": true
}
```

**响应示例**:
```json
{
  "code": 200,
  "msg": "Successfully enabled agent 'trading_agent'",
  "data": {
    "agent_name": "trading_agent",
    "enabled": true,
    "message": "Agent 'trading_agent' has been enabled successfully"
  }
}
```

**Section sources**
- [agent.py](file://python/valuecell/server/api/routers/agent.py#L1-L180)
- [agent.py](file://python/valuecell/server/api/schemas/agent.py)

## 对话管理API

对话管理API提供了对用户与智能体之间对话的完整管理功能。

### 获取对话列表
获取对话列表，支持可选的过滤和分页。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/conversations`
- **查询参数**:
  - `user_id` (字符串): 按用户ID过滤
  - `limit` (整数): 返回的对话数量（1-100）
  - `offset` (整数): 跳过的对话数量

### 获取对话历史
获取特定对话的完整消息历史。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/conversations/{conversation_id}/history`
- **路径参数**:
  - `conversation_id` (字符串): 对话ID

### 获取计划任务结果
获取特定对话的计划任务结果。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/conversations/{conversation_id}/scheduled-task-results`

### 删除对话
删除特定对话及其所有关联数据。

- **HTTP方法**: `DELETE`
- **URL**: `/api/v1/conversations/{conversation_id}`
- **路径参数**:
  - `conversation_id` (字符串): 要删除的对话ID

**Section sources**
- [conversation.py](file://python/valuecell/server/api/routers/conversation.py#L1-L148)
- [conversation.py](file://python/valuecell/server/api/schemas/conversation.py)

## 任务管理API

任务管理API提供了对后台任务的管理功能，包括取消任务等操作。

### 取消任务
通过ID取消任务，并更新对话历史中相应的计划任务控制器组件以反映"已取消"状态。

- **HTTP方法**: `POST`
- **URL**: `/api/v1/tasks/{task_id}/cancel`
- **路径参数**:
  - `task_id` (字符串): 要取消的任务ID

**响应示例**:
```json
{
  "code": 200,
  "msg": "Task cancelled successfully",
  "data": {
    "task_id": "task_123",
    "success": true,
    "cancelled_at": "2024-01-01T00:00:00Z"
  }
}
```

**Section sources**
- [task.py](file://python/valuecell/server/api/routers/task.py#L1-L44)
- [task.py](file://python/valuecell/server/api/schemas/task.py)

## 策略交易API

策略交易API提供了对交易策略的管理功能，包括策略、策略智能体和策略提示的管理。

### 聚合策略API路由器
策略API通过一个聚合路由器统一管理，将多个子路由器组合在一起：

- **核心策略端点** (`/strategies`): 管理交易策略
- **策略智能体端点** (`/strategies`): 管理策略智能体
- **策略提示端点** (`/strategies/prompts`): 管理策略提示

这种设计保持了逻辑上的分离，同时提供了统一的注册点。

**Section sources**
- [strategy_api.py](file://python/valuecell/server/api/routers/strategy_api.py#L1-L27)

## 流式API

流式API提供了实时流式响应功能，用于处理智能体查询。

### 流式查询智能体
流式返回智能体生成的内容，采用服务器发送事件(SSE)格式。

- **HTTP方法**: `POST`
- **URL**: `/api/v1/agents/stream`
- **请求体**:
```json
{
  "query": "当前股票市场情况如何？",
  "agent_name": "trading_agent",
  "conversation_id": "conv_123"
}
```

- **响应类型**: `text/event-stream`
- **响应头**:
```http
Cache-Control: no-cache
Connection: keep-alive
```

每个流式块的格式为SSE格式：
```
data: {"type": "text", "content": "分析中..."}
```

**Section sources**
- [agent_stream.py](file://python/valuecell/server/api/routers/agent_stream.py#L1-L64)

## 系统与配置API

系统与配置API提供了系统信息、健康检查和模型配置管理功能。

### 获取应用信息
获取ValueCell应用的基本信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/system/info`

### 健康检查
检查服务运行状态和版本信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/system/health`

### 获取默认股票代码
根据用户IP位置获取区域感知的默认股票代码。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/system/default-tickers`
- **查询参数**:
  - `region` (字符串): 区域覆盖（用于测试，cn或default）

### 模型配置管理
提供LLM模型配置的默认设置和管理功能。

#### 列出模型提供商
获取可用的模型提供商列表。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/models/providers`

#### 获取提供商详细信息
获取特定提供商的配置和模型信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/models/providers/{provider}`

#### 更新提供商配置
更新提供商的API密钥和主机配置。

- **HTTP方法**: `PUT`
- **URL**: `/api/v1/models/providers/{provider}/config`
- **请求体**:
```json
{
  "api_key": "your_api_key",
  "base_url": "https://api.example.com"
}
```

#### 设置默认提供商
设置默认的模型提供商。

- **HTTP方法**: `PUT`
- **URL**: `/api/v1/models/providers/default`
- **请求体**:
```json
{
  "provider": "openrouter"
}
```

**Section sources**
- [system.py](file://python/valuecell/server/api/routers/system.py#L1-L113)
- [models.py](file://python/valuecell/server/api/routers/models.py#L1-L486)

## 观察列表API

观察列表API提供了对金融资产观察列表的完整管理功能。

### 搜索资产
搜索金融资产（股票等）。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/watchlist/asset/search`
- **查询参数**:
  - `q` (字符串): 搜索查询
  - `asset_types` (字符串): 逗号分隔的资产类型
  - `exchanges` (字符串): 逗号分隔的交易所
  - `countries` (字符串): 逗号分隔的国家
  - `limit` (整数): 最大结果数
  - `language` (字符串): 本地化结果的语言

### 获取资产详情
获取特定资产的详细信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/watchlist/asset/{ticker}`
- **路径参数**:
  - `ticker` (字符串): 资产代码

### 获取资产价格
获取资产的当前价格信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/watchlist/asset/{ticker}/price`

### 获取历史价格
获取资产的历史价格数据。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/watchlist/asset/{ticker}/price/historical`
- **查询参数**:
  - `start_date` (字符串): 开始日期（UTC格式）
  - `end_date` (字符串): 结束日期（UTC格式）
  - `interval` (字符串): 数据间隔（1d, 1h, 5m等）
  - `language` (字符串): 本地化格式的语言

### 管理观察列表
提供创建、读取、更新和删除观察列表的功能。

- **获取观察列表**: `GET /api/v1/watchlist`
- **创建观察列表**: `POST /api/v1/watchlist`
- **获取特定观察列表**: `GET /api/v1/watchlist/{watchlist_name}`
- **删除观察列表**: `DELETE /api/v1/watchlist/{watchlist_name}`

### 管理观察列表中的资产
提供对观察列表中资产的管理功能。

- **添加资产**: `POST /api/v1/watchlist/asset`
- **移除资产**: `DELETE /api/v1/watchlist/asset/{ticker}`
- **更新资产备注**: `PUT /api/v1/watchlist/asset/{ticker}/notes`

**Section sources**
- [watchlist.py](file://python/valuecell/server/api/routers/watchlist.py#L1-L580)

## 用户配置文件API

用户配置文件API提供了对用户配置文件的管理功能。

### 创建用户配置文件
创建新的用户配置文件。

- **HTTP方法**: `POST`
- **URL**: `/api/v1/user/profile`
- **请求体**:
```json
{
  "category": "investment_strategy",
  "content": "我的投资策略是长期持有优质股票。"
}
```

### 获取用户配置文件
获取所有用户配置文件，可选择按类别过滤。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/user/profile`

### 获取配置文件摘要
获取按类别分组的用户配置文件摘要。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/user/profile/summary`

### 获取特定配置文件
通过ID获取特定用户配置文件。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/user/profile/{profile_id}`

### 更新用户配置文件
更新特定用户配置文件的内容。

- **HTTP方法**: `PUT`
- **URL**: `/api/v1/user/profile/{profile_id}`
- **请求体**:
```json
{
  "content": "更新后的投资策略。"
}
```

### 删除用户配置文件
删除特定用户配置文件。

- **HTTP方法**: `DELETE`
- **URL**: `/api/v1/user/profile/{profile_id}`

**Section sources**
- [user_profile.py](file://python/valuecell/server/api/routers/user_profile.py#L1-L246)

## 国际化API

国际化API提供了多语言支持和本地化功能。

### 获取i18n配置
获取当前的国际化配置信息。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/i18n/config`

### 获取支持的语言
获取系统支持的所有语言列表。

- **HTTP方法**: `GET`
- **URL**: `/api/v1/i18n/languages`

### 设置语言
设置用户的首选语言。

- **HTTP方法**: `PUT`
- **URL**: `/api/v1/i18n/language`
- **请求体**:
```json
{
  "language": "zh-CN"
}
```

### 设置时区
设置用户的首选时区。

- **HTTP方法**: `PUT`
- **URL**: `/api/v1/i18n/timezone`
- **请求体**:
```json
{
  "timezone": "Asia/Shanghai"
}
```

### 检测语言
基于Accept-Language头检测用户的首选语言。

- **HTTP方法**: `POST`
- **URL**: `/api/v1/i18n/detect-language`
- **请求体**:
```json
{
  "accept_language": "zh-CN,zh;q=0.9,en;q=0.8"
}
```

### 翻译文本
获取指定键和语言的翻译文本。

- **HTTP方法**: `POST`
- **URL**: `/api/v1/i18n/translate`
- **请求体**:
```json
{
  "key": "welcome_message",
  "language": "zh-CN"
}
```

### 格式化日期时间
根据用户的本地化设置格式化日期时间。

- **HTTP方法**: `POST`
- **URL**: `/api/v1/i18n/format/datetime`
- **请求体**:
```json
{
  "datetime": "2024-01-01T00:00:00Z",
  "format_type": "long"
}
```

### 格式化数字和货币
根据用户的本地化设置格式化数字和货币。

- **格式化数字**: `POST /api/v1/i18n/format/number`
- **格式化货币**: `POST /api/v1/i18n/format/currency`

### 用户设置
管理用户的国际化设置。

- **获取用户设置**: `GET /api/v1/i18n/user/settings`
- **更新用户设置**: `PUT /api/v1/i18n/user/settings`

**Section sources**
- [i18n.py](file://python/valuecell/server/api/routers/i18n.py#L1-L466)

## 错误处理

ValueCell API使用标准的HTTP状态码和统一的错误响应格式。

### 错误响应格式
```json
{
  "code": 404,
  "msg": "Agent with ID 123 not found",
  "data": null
}
```

### 常见错误代码
| 状态码 | 错误类型 | 描述 |
|--------|---------|------|
| 400 | BAD_REQUEST | 请求参数无效或缺失 |
| 401 | UNAUTHORIZED | 认证失败 |
| 403 | FORBIDDEN | 权限不足 |
| 404 | NOT_FOUND | 请求的资源不存在 |
| 422 | UNPROCESSABLE_ENTITY | 请求体验证失败 |
| 500 | INTERNAL_SERVER_ERROR | 服务器内部错误 |

### 异常处理
API通过异常处理器统一处理各种异常情况：

- `APIException`: 自定义API异常
- `RequestValidationError`: 请求验证异常
- `HTTPException`: HTTP异常
- `Exception`: 通用异常

**Section sources**
- [app.py](file://python/valuecell/server/api/app.py#L1-L242)
- [exceptions.py](file://python/valuecell/server/api/exceptions.py)

## 客户端实现指南

### 基本请求示例
使用curl获取应用信息：
```bash
curl -X GET "http://localhost:8000/api/v1/" \
  -H "X-User-ID: default_user"
```

### 流式API实现
实现流式API客户端时，需要处理SSE格式的响应：

```javascript
const response = await fetch('/api/v1/agents/stream', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-User-ID': 'default_user'
  },
  body: JSON.stringify({
    query: '分析AAPL股票',
    agent_name: 'trading_agent'
  })
});

const reader = response.body.getReader();
const decoder = new TextDecoder();

while(true) {
  const { done, value } = await reader.read();
  if (done) break;
  
  const chunk = decoder.decode(value);
  const lines = chunk.split('\n\n');
  for (const line of lines) {
    if (line.startsWith('data:')) {
      const data = JSON.parse(line.substring(5));
      console.log(data);
    }
  }
}
```

### 错误处理最佳实践
1. 检查HTTP状态码
2. 解析响应体中的错误信息
3. 实现重试机制（对于5xx错误）
4. 提供用户友好的错误消息

### 性能优化建议
1. 使用缓存减少重复请求
2. 批量请求替代多次单个请求
3. 合理设置请求频率
4. 压缩请求和响应数据

**Section sources**
- [agent_stream.py](file://python/valuecell/server/api/routers/agent_stream.py#L1-L64)
- [app.py](file://python/valuecell/server/api/app.py#L1-L242)