[根目录](../../../CLAUDE.md) > [frontend](../) > **src**

# 前端源码模块 (Frontend Source)

## 模块职责

前端源码模块是基于 React + TypeScript 的现代 Web 应用实现，使用 Tauri 构建跨平台桌面应用。提供用户友好的界面来管理金融智能体、配置交易策略、查看市场数据和监控投资组合。

## 入口与启动

### 应用入口
- **根组件**: `root.tsx`
- **路由配置**: `routes.ts`
- **启动命令**: `bun dev` (开发) / `bun build` (生产)

### 技术栈
- **React 19**: 使用最新版本的 React
- **React Router v7**: 文件系统路由
- **TypeScript**: 类型安全的 JavaScript
- **Tailwind CSS**: 实用优先的 CSS 框架
- **Tauri**: 跨平台桌面应用框架
- **Zustand**: 轻量级状态管理
- **React Query**: 服务器状态管理

## 应用结构

### 页面组件 (app/)

#### 1. 首页 (app/home/)
- `home.tsx`: 首页主组件
- `stock.tsx`: 股票页面
- `_layout.tsx`: 首页布局
- `components/`:
  - `stock-list.tsx`: 股票列表
  - `stock-details-list.tsx`: 股票详情列表
  - `sparkline-stock-list.tsx`: 迷你图表股票列表
  - `stock-search-modal.tsx`: 股票搜索模态框
  - `agent-suggestions-list.tsx`: 智能体建议列表
  - `agent-task-cards.tsx`: 智能体任务卡片

#### 2. 智能体 (app/agent/)
- `config.tsx`: 智能体配置
- `components/`:
  - `strategy-items/`: 策略项目组件
  - `control-panel/`: 控制面板
  - `message-list/`: 消息列表
  - `prompt-editor/`: 提示词编辑器

#### 3. 市场 (app/market/)
- `agents.tsx`: 智能体市场
- `components/agent-card.tsx`: 智能体卡片

#### 4. 排行榜 (app/rank/)
- `board.tsx`: 排行榜页面
- `components/strategy-remote-modal.tsx`: 策略远程模态框

#### 5. 设置 (app/setting/)
- `general.tsx`: 通用设置
- `memory.tsx`: 内存设置
- `models.tsx`: 模型设置
- `_layout.tsx`: 设置布局
- `components/`:
  - `models/`: 模型相关组件
  - `memory/`: 内存管理组件

### 状态管理 (store/)

- `system-store.ts`: 系统状态
- `settings-store.ts`: 设置状态
- `agent-store.ts`: 智能体状态
- `plugin/tauri-store-state.ts`: Tauri 插件状态

### 自定义组件 (components/valuecell/)

#### UI 基础组件
- `ui/`: 基于 Radix UI 的组件库
  - `alert-dialog.tsx`: 警告对话框
  - `avatar.tsx`: 头像组件
  - `button.tsx`: 按钮组件
  - `card.tsx`: 卡片组件
  - `dialog.tsx`: 对话框
  - `dropdown-menu.tsx`: 下拉菜单
  - `input.tsx`: 输入框
  - `sidebar.tsx`: 侧边栏
  - `table.tsx`: 表格
  - `tabs.tsx`: 标签页
  - 等等...

#### 业务组件
- `app/`: 应用级组件
  - `app-sidebar.tsx`: 应用侧边栏
  - `app-conversation-sheet.tsx`: 对话面板
  - `backend-health-check.tsx`: 后端健康检查
  - `auto-update-check.tsx`: 自动更新检查

- `modal/`: 模态框组件
  - `login-modal.tsx`: 登录模态框
  - `copy-strategy-modal.tsx`: 复制策略模态框

- `form/`: 表单组件
  - `trading-strategy-form.tsx`: 交易策略表单
  - `ai-model-form.tsx`: AI 模型表单
  - `exchange-form.tsx`: 交易所表单
  - `field/`: 表单字段组件

- `charts/`: 图表组件
  - `sparkline.tsx`: 迷你图表
  - `model-multi-line.tsx`: 多线模型图
  - `mini-sparkline.tsx`: 迷你闪电图

- `renderer/`: 渲染器组件
  - `markdown-renderer.tsx`: Markdown 渲染
  - `chat-conversation-renderer.tsx`: 对话渲染
  - `tool-call-renderer.tsx`: 工具调用渲染
  - `reasoning-renderer.tsx`: 推理过程渲染

- `icon/`: 图标组件
  - `svg-icon.tsx`: SVG 图标
  - `png-icon.tsx`: PNG 图标
  - `agent-avatar.tsx`: 智能体头像

### 工具库 (lib/)

- `api-client.ts`: API 客户端
- `sse-client.ts`: SSE 客户端
- `agent-store.ts`: 智能体存储
- `utils.ts`: 工具函数
- `tracker.ts`: 追踪器
- `time.ts`: 时间处理

### Hooks (hooks/)

- `use-sse.ts`: SSE 连接 Hook
- `use-tauri-info.ts`: Tauri 信息 Hook
- `use-mobile.ts`: 移动端检测
- `use-form.tsx`: 表单 Hook
- `use-debounce.ts`: 防抖 Hook
- `use-chart-resize.ts`: 图表调整 Hook
- `use-update-toast.tsx`: 更新提示 Hook

### 类型定义 (types/)

- `agent.ts`: 智能体类型
- `stock.ts`: 股票类型
- `strategy.ts`: 策略类型
- `conversation.ts`: 对话类型
- `setting.ts`: 设置类型
- `renderer.ts`: 渲染器类型
- `chart.ts`: 图表类型
- `system.ts`: 系统类型

### 提供者 (provider/)

- `tracker-provider.tsx`: 追踪器提供者
- `multi-section-provider.tsx`: 多段提供者

### 常量 (constants/)

- `api.ts`: API 常量
- `agent.ts`: 智能体常量
- `stock.ts`: 股票常量
- `schema.ts`: Schema 常量
- `icons.ts`: 图标常量

### 资源 (assets/)

- `svg/`: SVG 图标资源
  - `sidebar/`: 侧边栏图标
  - `agent-suggest/`: 智能体建议图标
- `png/`: PNG 图标资源

## 样式系统

### 全局样式
- `global.css`: 全局 CSS 样式
- 使用 Tailwind CSS 进行样式管理
- 支持暗色/亮色主题切换

### 组件样式
- 使用 Tailwind 的 @apply 指令
- CSS 变量支持主题切换
- 响应式设计

## 国际化 (i18n)

### 支持的语言 (v0.1.20 增强)
- 🇨🇳 **中文简体** (zh_CN)
- 🇹🇼 **中文繁体** (zh_TW)
- 🇺🇸 **英语** (en)
- 🇯🇵 **日语** (ja) - v0.1.20 新增

### i18n 实现
- **配置文件**: `i18n/index.ts` - 国际化配置
- **语言包**: `i18n/locales/` - 各语言翻译文件
  - `en.json`: 英语翻译
  - `zh_CN.json`: 简体中文翻译
  - `zh_TW.json`: 繁体中文翻译
  - `ja.json`: 日语翻译（v0.1.20 新增）
- **语言切换**: 通过通用设置页面进行切换
- **持久化**: 语言选择保存在本地设置中

### v0.1.20 i18n 增强
- 扩展了多个组件和模态框的国际化覆盖
- 新增日文语言支持
- 完善了表单验证和错误提示的多语言
- 增强了策略、模态框等业务组件的国际化

## 性能优化

### 代码分割
- React Router v7 自动代码分割
- 懒加载页面组件

### 状态优化
- Zustand 进行轻量级状态管理
- React Query 缓存 API 响应

### 渲染优化
- React.memo 优化组件渲染
- useMemo 和 useCallback 优化计算

## 测试策略

### 测试框架
- 使用 React Testing Library
- Jest 作为测试运行器（待配置）

### 测试覆盖
- 组件单元测试
- Hook 测试
- 集成测试

## 构建与部署

### 开发环境
```bash
bun dev        # 开发服务器
bun typecheck  # 类型检查
bun lint       # 代码检查
```

### 生产构建
```bash
bun build      # 构建 Web 应用
bun tauri build # 构建桌面应用
```

### 构建优化
- Vite 进行快速构建
- 代码压缩和混淆
- Tree shaking 移除无用代码

## 常见问题 (FAQ)

### Q: 如何添加新页面？
A: 在 `app/` 目录下创建新的路由文件，React Router v7 会自动识别。

### Q: 如何添加新的 UI 组件？
A: 在 `components/valuecell/ui/` 下创建新组件，使用 Radix UI 作为基础。

### Q: 如何管理全局状态？
A: 使用 Zustand store，参考现有 store 文件的实现。

## 相关文件清单

### 应用入口
- `root.tsx`: 应用根组件
- `routes.ts`: 路由配置

### 页面组件
- `app/home/`: 首页相关
- `app/agent/`: 智能体页面
- `app/market/`: 市场页面
- `app/rank/`: 排行榜页面
- `app/setting/`: 设置页面

### 状态管理
- `store/`: Zustand stores
- `hooks/`: 自定义 Hooks

### 组件库
- `components/valuecell/ui/`: UI 基础组件
- `components/valuecell/`: 业务组件

### 工具和配置
- `lib/`: 工具函数
- `types/`: 类型定义
- `constants/`: 常量定义

## 变更记录 (Changelog)
- 2026-01-10: 更新到 v0.1.20，新增日文支持，扩展 i18n 覆盖范围
- 2025-12-05: 初始文档创建，梳理前端源码结构