[根目录](../../../CLAUDE.md) > [python](../../) > [valuecell](../) > **agents**

# 智能体模块 (Agents)

## 模块职责

智能体模块包含了 ValueCell 平台的所有专业金融智能体实现。每个智能体专注于特定的金融领域，如深度研究、自动交易、新闻分析等。模块采用插件化设计，支持灵活扩展新智能体。

## 入口与启动

### 智能体注册入口
- `__init__.py`: 智能体模块初始化和导出
- `common/trading/base_agent.py`: 交易类智能体基类

### 智能体列表
1. **研究智能体** (`research_agent/`)
2. **新闻智能体** (`news_agent/`)
3. **网格交易智能体** (`grid_agent/`)
4. **提示策略智能体** (`prompt_strategy_agent/`)

## 对外接口

### 1. 研究智能体 (research_agent)
**主文件**: `research_agent/core.py`
**功能**: 自动检索和分析基本面文档，生成准确的数据洞察和可解释的摘要

**核心能力**:
- 文档检索和分析
- 基本面数据处理
- 知识库管理
- 研究报告生成

**数据源**:
- `sources.py`: 数据源适配器
- `rootdata.py`: 根数据源处理

### 2. 新闻智能体 (news_agent)
**主文件**: `news_agent/core.py`
**功能**: 支持个性化定时新闻推送，实时跟踪关键信息

**核心能力**:
- 新闻抓取和分析
- 个性化推送
- 关键词追踪
- 情绪分析

### 3. 网格交易智能体 (grid_agent)
**主文件**: `grid_agent/grid_agent.py`
**功能**: 实现网格交易策略，自动执行交易计划

**核心能力**:
- 网格策略配置
- 自动下单
- 风险控制
- 盈亏统计

### 4. 提示策略智能体 (prompt_strategy_agent)
**主文件**: `prompt_strategy_agent/core.py`
**功能**: 基于提示词的交易策略生成

**策略模板**:
- `templates/default.txt`: 默认策略
- `templates/aggressive.txt`: 激进策略
- `templates/insane.txt`: 高风险策略

## 通用交易框架 (common/trading/)

### 核心组件
- **base_agent.py**: 交易智能体基类
- **coordinator.py**: 交易协调器
- **runtime.py**: 运行时环境

### 交易执行 (execution/)
- `interfaces.py`: 执行接口定义
- `ccxt_trading.py`: CCXT 交易实现
- `paper_trading.py`: 模拟交易
- `exchanges.py`: 交易所适配
- `factory.py`: 执行器工厂

### 决策引擎 (decision/)
- `interfaces.py`: 决策接口
- `prompt_based/`: 基于提示词的决策
  - `composer.py`: 决策组合器
  - `system_prompt.py`: 系统提示词
- `grid_composer/`: 网格决策器
  - `grid_composer.py`: 网格决策实现
  - `llm_param_advisor.py`: LLM 参数建议

### 数据管理 (data/)
- `interfaces.py`: 数据接口
- `market.py`: 市场数据处理

### 特征工程 (features/)
- `interfaces.py`: 特征接口
- `candle.py`: K线数据处理
- `market_snapshot.py`: 市场快照
- `pipeline.py`: 特征管道

### 历史记录 (history/)
- `interfaces.py`: 历史接口
- `recorder.py`: 记录器
- `digest.py`: 摘要生成
- `in_memory.py`: 内存存储

### 投资组合 (portfolio/)
- `interfaces.py`: 投资组合接口
- `in_memory.py`: 内存实现

## 数据源管理 (sources/)

### 支持的数据源
- `rootdata.py`: 根数据源
- `__init__.py`: 数据源管理

## 智能体卡片配置

### 卡片文件位置
`../../configs/agent_cards/`
- `research_agent.json`: 研究智能体配置
- `news_agent.json`: 新闻智能体配置
- `grid_agent.json`: 网格智能体配置
- `prompt_strategy_agent.json`: 提示策略智能体配置

## 关键依赖与配置

### 外部依赖
- `ccxt`: 加密货币交易库
- `yfinance`: Yahoo Finance API
- `akshare`: A股数据源
- `baostock`: 宝塔股票数据
- `crawl4ai`: 网页爬虫
- `agno`: AI 智能体框架
- `lancedb`: 向量数据库
- `edgartools`: EDGAR 文档工具

### 配置文件
- `../../configs/agents/`: 智能体配置
  - `research_agent.yaml`
  - `news_agent.yaml`
  - `super_agent.yaml`

## 测试与质量

### 测试目录
- `research_agent/tests/`: 研究智能体测试
- `common/trading/`: 交易框架测试

### 测试覆盖
- 单元测试
- 集成测试
- 容错测试
- 性能测试

## 常见问题 (FAQ)

### Q: 如何添加新的交易智能体？
A: 1. 继承 `common/trading/base_agent.py`
   2. 实现必要的决策和执行接口
   3. 创建智能体卡片配置
   4. 添加到智能体注册表

### Q: 如何支持新的交易所？
A: 在 `execution/exchanges.py` 中添加新的交易所适配器，实现统一的交易接口。

### Q: 如何配置数据源？
A: 修改 `sources/` 目录下的数据源适配器，或在配置文件中添加新的数据源。

## 相关文件清单

### 智能体实现
- `research_agent/core.py`: 研究智能体核心
- `news_agent/core.py`: 新闻智能体核心
- `grid_agent/grid_agent.py`: 网格交易智能体
- `prompt_strategy_agent/core.py`: 提示策略智能体

### 通用框架
- `common/trading/base_agent.py`: 交易基类
- `common/trading/_internal/coordinator.py`: 内部协调器
- `common/trading/_internal/runtime.py`: 运行时环境
- `common/trading/constants.py`: 常量定义

### 执行层
- `common/trading/execution/ccxt_trading.py`: CCXT 交易实现
- `common/trading/execution/paper_trading.py`: 模拟交易
- `common/trading/execution/factory.py`: 执行器工厂

### 决策层
- `common/trading/decision/prompt_based/composer.py`: 提示词决策
- `common/trading/decision/grid_composer/grid_composer.py`: 网格决策

### 数据层
- `common/trading/data/market.py`: 市场数据
- `common/trading/features/candle.py`: K线特征
- `common/trading/history/recorder.py`: 历史记录

## 变更记录 (Changelog)
- 2026-01-10: 更新到 v0.1.20，智能体支持新的模型提供商（Ollama）和 i18n 增强
- 2025-12-05: 初始文档创建，梳理智能体模块结构