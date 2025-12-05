[根目录](../../../CLAUDE.md) > [python](../../) > [valuecell](../) > **adapters**

# 数据适配器模块 (Adapters)

## 模块职责

数据适配器模块为 ValueCell 提供统一的数据源接口，支持多个金融数据提供商的接入。该模块通过适配器模式抽象不同数据源的差异，提供统一的资产查询、价格获取、历史数据等功能，并支持智能故障转移和数据源优先级管理。

## 入口与启动

### 核心管理器
- **适配器管理器**: `assets/manager.py` - AdapterManager
- **观察列表管理器**: `assets/manager.py` - WatchlistManager
- **基础适配器**: `assets/base.py` - BaseDataAdapter

### 初始化流程
```python
from valuecell.adapters import get_adapter_manager, get_watchlist_manager

# 获取全局管理器实例
adapter_manager = get_adapter_manager()
watchlist_manager = get_watchlist_manager()

# 配置数据源
adapter_manager.configure_yfinance()
adapter_manager.configure_akshare()
adapter_manager.configure_baostock()
```

## 对外接口

### 1. 适配器管理接口
- `register_adapter(adapter)`: 注册新的数据适配器
- `get_available_adapters()`: 获取可用适配器列表
- `get_adapter_for_ticker(ticker)`: 根据代码获取最佳适配器
- `get_adapters_for_exchange(exchange)`: 获取交易所支持的适配器

### 2. 资产查询接口
- `search_assets(query)`: 搜索资产（支持模糊匹配和智能去重）
- `get_asset_info(ticker)`: 获取资产详细信息
- `get_real_time_price(ticker)`: 获取实时价格
- `get_multiple_prices(tickers)`: 批量获取价格
- `get_historical_prices(ticker, start, end)`: 获取历史价格数据

### 3. 观察列表接口
- `create_watchlist(user_id, name)`: 创建观察列表
- `add_asset_to_watchlist(user_id, ticker)`: 添加资产到观察列表
- `get_watchlist_prices(user_id)`: 获取观察列表价格

## 关键依赖与配置

### 支持的数据源
- **Yahoo Finance**: 全球股市数据（通过 yfinance 库）
- **AKShare**: 中国股市数据
- **BaoStock**: 中国股市历史数据
- **可扩展架构**: 易于添加新的数据源

### 核心组件
- **AdapterManager**: 协调多个适配器，处理路由和故障转移
- **WatchlistManager**: 管理用户观察列表和投资组合
- **BaseDataAdapter**: 所有适配器的抽象基类
- **AdapterCapability**: 描述适配器支持的资产类型和交易所

### 交易所支持
- **美股**: NASDAQ, NYSE, AMEX
- **A股**: SSE(上交所), SZSE(深交所), BSE(北交所)
- **港股**: HKEX
- **加密货币**: CRYPTO

## 数据模型

### 核心数据结构
- **Asset**: 资产基本信息（代码、名称、类型等）
- **AssetPrice**: 价格数据（价格、变化率、时间戳等）
- **AssetSearchResult**: 搜索结果（包含相关性评分）
- **Watchlist**: 观察列表（用户资产集合）

### 智能特性
- **智能去重**: 跨交易所重复资产自动去重，优先选择主要交易所
- **故障转移**: 主数据源失败时自动切换到备用数据源
- **缓存机制**: 适配器选择缓存，提高查询效率
- **并发查询**: ThreadPoolExecutor 并发查询多个数据源

## 测试与质量

### 测试覆盖
- `assets/tests/test_adapters_comparison.py`: 适配器对比测试
- 单元测试覆盖所有适配器接口
- 集成测试验证故障转移机制

### 错误处理
- 统一的异常处理机制
- 超时控制和重试策略
- 详细的错误日志记录

## 性能优化

### 并发处理
- 多线程并发查询不同数据源
- 批量价格获取优化
- 连接池和会话复用

### 缓存策略
- Ticker 到适配器的映射缓存
- 查询结果缓存（可配置 TTL）
- 智能预加载常用数据

## 扩展指南

### 添加新数据源
1. 继承 `BaseDataAdapter` 创建新适配器
2. 实现所有必需的抽象方法
3. 在 AdapterManager 中注册新适配器
4. 添加相应的测试用例

### 自定义路由策略
- 重写 `_rebuild_routing_table()` 方法
- 实现自定义的适配器选择逻辑
- 添加新的故障转移策略

## 常见问题 (FAQ)

### Q: 如何添加新的数据源？
A: 创建新的适配器类继承 BaseDataAdapter，实现必需方法，然后在管理器中注册。

### Q: 如何处理 API 限制？
A: 适配器自动处理请求频率限制，支持错误重试和指数退避。

### Q: 数据如何同步？
A: 实时数据通过 API 获取，历史数据支持批量下载和增量更新。

### Q: 如何确保数据准确性？
A: 通过多数据源交叉验证，智能选择最可靠的数据源。

## 相关文件清单

### 核心模块
- `assets/manager.py`: 适配器和观察列表管理器
- `assets/base.py`: 基础适配器抽象类
- `assets/types.py`: 数据类型定义
- `assets/i18n_integration.py`: 国际化集成

### 数据适配器实现
- `assets/yfinance_adapter.py`: Yahoo Finance 适配器
- `assets/akshare_adapter.py`: AKShare 适配器
- `assets/baostock_adapter.py`: BaoStock 适配器

### 工厂和模型
- `models/factory.py`: 适配器工厂
- `models/__init__.py`: 模型导出

### 测试文件
- `assets/tests/test_adapters_comparison.py`: 适配器对比测试

## 变更记录 (Changelog)
- 2025-12-05: 初始文档创建，梳理数据适配器模块结构和接口