[根目录](../CLAUDE.md) > **configs**

# 配置文件系统 (Configs)

## 模块职责

配置文件系统为 ValueCell 提供集中化的配置管理，支持：
- 模型提供商配置管理
- 智能体参数配置
- 国际化语言包
- 环境变量覆盖
- 分层配置优先级

## 配置结构

### 主配置文件
- **config.yaml**: 系统主配置文件
- 优先级：环境变量 > .env 文件 > YAML 配置

### 配置目录组织
```
configs/
├── config.yaml              # 主配置文件
├── agents/                  # 智能体配置
│   ├── super_agent.yaml
│   ├── research_agent.yaml
│   └── news_agent.yaml
├── providers/               # 模型提供商配置
│   ├── openai.yaml
│   ├── openrouter.yaml
│   ├── google.yaml
│   ├── azure.yaml
│   └── deepseek.yaml
├── agent_cards/            # 智能体卡片（JSON）
│   ├── grid_agent.json
│   ├── investment_research_agent.json
│   └── news_agent.json
└── locales/                # 国际化语言包
    ├── en-US.json
    ├── zh-Hans.json
    └── language_list.json
```

## 主配置文件 (config.yaml)

### 配置优先级
1. **环境变量** (最高优先级)
2. **.env 文件**
3. **YAML 文件** (最低优先级，系统默认)

### 模型提供商配置
```yaml
models:
  # 主要提供商
  primary_provider: "openrouter"

  # 提供商注册表
  providers:
    openrouter:
      config_file: "providers/openrouter.yaml"
      api_key_env: "OPENROUTER_API_KEY"

    siliconflow:
      config_file: "providers/siliconflow.yaml"
      api_key_env: "SILICONFLOW_API_KEY"
```

### 智能体配置
```yaml
agents:
  super_agent:
    config_file: "agents/super_agent.yaml"

  research_agent:
    config_file: "agents/research_agent.yaml"
```

## 智能体配置

### Super Agent 配置 (agents/super_agent.yaml)
- **功能**: 作为编排层，在规划前分流用户意图
- **能力**: Web 搜索、网络爬取、会话管理
- **环境变量覆盖**: 支持运行时配置

### 其他智能体
- **研究智能体**: 投资研究和分析
- **新闻智能体**: 金融新闻聚合和分析
- **策略智能体**: 交易策略生成和执行

## 模型提供商配置

### 支持的提供商
1. **OpenRouter**: 多模型路由服务
2. **OpenAI**: GPT 系列模型
3. **Google**: Gemini 系列模型
4. **Azure**: Azure OpenAI 服务
5. **SiliconFlow**: 国产模型平台
6. **DeepSeek**: DeepSeek AI 模型
7. **DashScope**: 阿里云通义千问
8. **OpenAI Compatible**: 兼容 OpenAI API 的服务

### 配置格式
每个提供商配置文件包含：
- API 端点信息
- 模型列表和参数
- 请求限制和重试策略
- 特殊参数配置

## 智能体卡片 (agent_cards)

### JSON 格式配置
- 用于前端显示智能体信息
- 包含智能体描述、能力标签
- 支持动态加载和更新

### 示例卡片
```json
{
  "id": "grid_agent",
  "name": "网格交易智能体",
  "description": "自动化网格交易策略执行",
  "capabilities": ["grid_trading", "risk_management", "automated_execution"],
  "category": "trading"
}
```

## 国际化配置 (locales)

### 支持的语言
- **en-US**: 美式英语
- **en-GB**: 英式英语
- **zh-Hans**: 简体中文
- **zh-Hant**: 繁体中文

### 语言包结构
- 界面文本翻译
- 错误消息本地化
- 日期和数字格式
- 货币和地区设置

### 语言列表
```json
{
  "languages": [
    {"code": "en-US", "name": "English (US)"},
    {"code": "zh-Hans", "name": "简体中文"},
    {"code": "zh-Hant", "name": "繁體中文"}
  ]
}
```

## 环境变量覆盖

### 配置映射
系统支持通过环境变量覆盖 YAML 配置：

```yaml
# 智能体环境变量映射
env_overrides:
  SUPER_AGENT_MODEL_ID: "models.primary.model_id"
  SUPER_AGENT_PROVIDER: "models.primary.provider"
  SUPER_AGENT_TEMPERATURE: "models.primary.parameters.temperature"
```

### 使用方式
```bash
# 设置主模型
export SUPER_AGENT_MODEL_ID="anthropic/claude-sonnet"

# 设置提供商
export SUPER_AGENT_PROVIDER="openrouter"
```

## 配置加载机制

### 加载顺序
1. 加载默认 YAML 配置
2. 应用 .env 文件覆盖
3. 应用环境变量覆盖
4. 验证配置完整性

### 配置验证
- 必需字段检查
- 类型验证
- 值范围验证
- 依赖关系验证

## 动态配置

### 热更新支持
- 智能体参数动态调整
- 模型切换
- 日志级别修改

### 配置缓存
- 减少文件读取开销
- 配置变更检测
- 版本管理

## 安全配置

### 敏感信息
- API 密钥通过环境变量注入
- 不在配置文件中存储密码
- 支持密钥轮换

### 访问控制
- 配置文件权限管理
- 敏感配置加密存储
- 审计日志记录

## 最佳实践

### 配置管理
1. 使用版本控制管理配置文件
2. 不同环境使用不同的配置文件
3. 定期审查和更新配置
4. 文档化所有配置项

### 环境变量
1. 使用描述性的环境变量名
2. 设置合理的默认值
3. 在 .env.example 中记录所有变量
4. 敏感信息通过密钥管理服务

## 常见问题 (FAQ)

### Q: 如何添加新的模型提供商？
A: 在 providers/ 目录创建新的 YAML 配置文件，在 config.yaml 中注册。

### Q: 如何实现配置热更新？
A: 使用文件监控机制，检测配置变更并重新加载。

### Q: 如何管理不同环境的配置？
A: 使用环境变量或不同的配置文件名进行区分。

### Q: 如何保护 API 密钥安全？
A: 使用环境变量或密钥管理服务，不要提交到版本控制。

## 相关文件清单

### 主配置
- `config.yaml`: 系统主配置

### 智能体配置
- `agents/super_agent.yaml`: 超级智能体配置
- `agents/research_agent.yaml`: 研究智能体配置
- `agents/news_agent.yaml`: 新闻智能体配置

### 提供商配置
- `providers/openrouter.yaml`: OpenRouter 配置
- `providers/openai.yaml`: OpenAI 配置
- `providers/google.yaml`: Google 配置
- `providers/azure.yaml`: Azure 配置

### 智能体卡片
- `agent_cards/*.json`: 智能体信息卡片

### 国际化
- `locales/*.json`: 多语言语言包

## 变更记录 (Changelog)
- 2025-12-05: 初始文档创建，梳理配置系统结构和功能