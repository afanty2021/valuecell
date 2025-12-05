[根目录](../CLAUDE.md) > **docs**

# 项目文档 (Docs)

## 模块职责

文档模块为 ValueCell 项目提供全面的文档支持，包括：
- 核心架构说明
- 配置指南
- 开发者指南
- API 文档
- 部署说明

## 文档结构

### 核心文档
- **CORE_ARCHITECTURE.md**: 系统核心架构详解
- **CONFIGURATION_GUIDE.md**: 配置系统使用指南
- **CONTRIBUTE_AN_AGENT.md**: 贡献智能体开发指南

### 文档组织
```
docs/
├── CORE_ARCHITECTURE.md      # 核心架构文档
├── CONFIGURATION_GUIDE.md    # 配置指南
└── CONTRIBUTE_AN_AGENT.md    # 智能体贡献指南
```

## 核心架构文档 (CORE_ARCHITECTURE.md)

### 内容概要
详细介绍 ValueCell 核心模块的协作机制：

#### 核心特性
- **Super Agent 分流**: 轻量级 Super Agent 先分析用户输入
- **异步可重入编排器**: 流式响应，后台持续处理
- **支持人机协作的规划器**: 通过 UserInputRequest 暂停和恢复
- **流式管道**: A2A 状态事件 → ResponseRouter → ResponseBuffer
- **Agent2Agent 集成**: 通过 a2a-sdk 调用远程智能体
- **会话记忆**: 内存/SQLite 存储，支持历史恢复

#### 服务交互图
使用 Mermaid 图表展示服务间的交互关系：
- Orchestrator 作为中心枢纽
- SuperAgentService、PlanService、TaskExecutor 协作
- EventResponseService 处理路由和缓冲
- ConversationService 管理状态

#### 高级流程图
展示完整的用户请求处理流程：
- 用户输入 → Super Agent 分流
- 直接回答或交由规划器处理
- HITL（人机协作）交互
- 任务执行和 A2A 调用
- 响应流式返回

## 配置指南 (CONFIGURATION_GUIDE.md)

### 指南内容
- 配置文件系统说明
- 环境变量设置
- 模型提供商配置
- 智能体参数调整
- 常见配置问题解决

### 配置最佳实践
- 分层配置管理
- 安全配置建议
- 性能调优指南
- 故障排除方法

## 智能体贡献指南 (CONTRIBUTE_AN_AGENT.md)

### 开发流程
1. 智能体设计原则
2. 代码结构规范
3. 接口实现要求
4. 测试用例编写
5. 文档编写规范
6. 提交流程说明

### 技术要求
- 继承基础智能体类
- 实现必需接口
- 异步编程模式
- 错误处理机制
- 日志记录规范

## 文档维护

### 更新原则
- 代码变更同步更新文档
- 定期审查文档准确性
- 保持示例代码可运行
- 多语言文档维护

### 贡献指南
- 使用清晰的标题结构
- 提供代码示例
- 包含图表说明
- 标注最后更新时间

## 最佳实践

### 文档编写
1. **结构化组织**: 使用清晰的目录和标题
2. **示例丰富**: 提供实际可用的代码示例
3. **图文并茂**: 使用图表辅助说明
4. **版本标识**: 标注文档适用的版本

### 技术文档
1. **API 文档**: 完整的接口说明
2. **架构图**: 清晰的系统架构图
3. **流程图**: 详细的处理流程
4. **配置示例**: 实际的配置文件示例

## 扩展计划

### 计划添加的文档
- **API_REFERENCE.md**: API 接口参考
- **DEPLOYMENT_GUIDE.md**: 部署指南
- **TROUBLESHOOTING.md**: 故障排除指南
- **FAQ.md**: 常见问题解答
- **CHANGELOG.md**: 版本变更日志

### 多语言支持
- 英文文档（当前）
- 中文文档（规划中）
- 其他语言（根据需求）

## 文档工具

### 推荐工具
- **Markdown**: 主要文档格式
- **Mermaid**: 图表绘制
- **MkDocs**: 文档生成（可选）
- **GitBook**: 在线文档（可选）

### 本地预览
```bash
# 使用 Markdown 预览工具
markdown-preview docs/CORE_ARCHITECTURE.md

# 或使用 VS Code 预览
code docs/CORE_ARCHITECTURE.md
```

## 质量保证

### 审查流程
- 技术准确性审查
- 文档结构检查
- 示例代码验证
- 链接有效性检查

### 持续改进
- 收集用户反馈
- 定期更新内容
- 优化文档结构
- 提升可读性

## 常见问题 (FAQ)

### Q: 如何贡献新文档？
A: 创建 Markdown 文件，遵循现有文档格式，提交 PR。

### Q: 文档如何保持更新？
A: 代码变更时同步更新文档，定期审查和修订。

### Q: 如何添加多语言支持？
A: 创建对应语言的目录和文件，保持结构一致。

## 相关资源

### 内部链接
- [根目录 CLAUDE.md](../CLAUDE.md)
- [配置系统](../configs/CLAUDE.md)
- [核心模块](../python/valuecell/core/CLAUDE.md)

### 外部资源
- Markdown 语法指南
- Mermaid 图表文档
- Tauri 官方文档
- FastAPI 文档

## 变更记录 (Changelog)
- 2025-12-05: 初始文档创建，梳理文档模块结构