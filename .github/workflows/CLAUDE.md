[根目录](../../CLAUDE.md) > [.github](../) > **workflows**

# CI/CD 工作流模块 (GitHub Actions Workflows)

## 模块职责

CI/CD 工作流模块负责自动化项目的构建、测试和部署流程。通过 GitHub Actions 实现：
- 代码质量检查（linting、格式化）
- 类型检查和构建验证
- 跨平台桌面应用构建
- 自动化测试执行
- 发布流程自动化

## 工作流列表

### 1. 前端检查 (frontend.yml)
负责前端代码质量检查和构建验证：
- **触发条件**: 推送到 main 分支或针对 frontend/ 目录的 PR
- **检查项**:
  - Biome 代码格式化和 linting
  - TypeScript 类型检查
  - 项目构建验证
- **运行环境**: Ubuntu Latest

### 2. Python 检查 (python.yml)
负责 Python 代码质量检查：
- **触发条件**: 推送到 main 分支或针对 python/ 目录的 PR
- **检查项**:
  - Ruff linting（代码风格检查）
  - Ruff format（代码格式化）
  - isort（导入排序）
  - 单元测试执行
  - 测试覆盖率检查（要求 >90%）
- **运行环境**: Ubuntu Latest

### 3. macOS 构建 (mac_build.yml)
负责 macOS 平台的桌面应用构建：
- **触发条件**:
  - 推送版本标签（v*）
  - 手动触发（workflow_dispatch）
- **支持架构**:
  - Apple Silicon (aarch64-apple-darwin)
  - Intel (x86_64-apple-darwin)
- **特殊功能**:
  - Apple 开发者证书导入
  - 应用代码签名
  - 自动发布到 GitHub Releases
- **产物**: .dmg 安装包

### 4. Windows 构建 (win_build.yml)
负责 Windows 平台的桌面应用构建：
- **触发条件**:
  - 推送版本标签（v*）
  - 手动触发（workflow_dispatch）
- **支持架构**: x86_64-pc-windows-msvc
- **产物**:
  - .msi 安装包
  - .exe 便携版

## 关键配置

### 环境变量和密钥
- `APPLE_ID`: Apple 开发者 ID
- `APPLE_ID_PASSWORD`: Apple ID 密码
- `APPLE_CERTIFICATE`: Apple 开发者证书（Base64）
- `APPLE_CERTIFICATE_PASSWORD`: 证书密码
- `APPLE_TEAM_ID`: Apple 团队 ID
- `TAURI_SIGNING_PRIVATE_KEY`: Tauri 签名私钥
- `TAURI_SIGNING_PRIVATE_KEY_PASSWORD`: 私钥密码

### 构建工具版本
- ** Bun**: 从 package.json 自动读取
- ** Rust**: 使用 stable 工具链
- ** UV**: 0.9.9（用于 Python 包管理）

## 优化特性

### 缓存策略
- Rust 依赖缓存（使用 swatinem/rust-cache）
- Python 依赖缓存（通过 uv）
- Node.js 依赖缓存（通过 bun）

### 并行构建
- macOS 支持多架构并行构建
- Linux 和 Windows 可独立运行

### 安全措施
- 密码和敏感信息使用 GitHub Secrets
- 证书信息添加 mask 保护
- 构建产物自动上传并设置过期时间

## 常见问题 (FAQ)

### Q: 如何添加新的构建平台？
A: 创建新的 workflow 文件，参考现有的 mac_build.yml 和 win_build.yml，调整目标平台和构建参数。

### Q: 如何修改代码质量检查标准？
A: 在相应的 workflow 文件中修改命令参数，例如调整 diff-cover 的覆盖率阈值。

### Q: 构建失败如何调试？
A: 查看 Actions 日志，可以下载失败的构建产物进行本地调试。

### Q: 如何手动触发构建？
A: 访问 GitHub Actions 页面，选择相应的工作流，点击"Run workflow"按钮。

## 相关文件清单

- `frontend.yml`: 前端 CI 工作流
- `python.yml`: Python CI 工作流
- `mac_build.yml`: macOS 构建工作流
- `win_build.yml`: Windows 构建工作流

## 变更记录 (Changelog)
- 2025-12-05: 初始文档创建，记录 CI/CD 工作流配置