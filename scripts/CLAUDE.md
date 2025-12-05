[根目录](../CLAUDE.md) > **scripts**

# 构建脚本模块 (Build Scripts)

## 模块职责

构建脚本模块提供项目的自动化构建、环境准备和部署功能，包括：
- 项目启动和停止
- 依赖管理和环境准备
- 构建前清理工作
- 跨平台支持

## 脚本列表

### 1. 项目启动脚本 (start.sh)
主启动脚本，用于开发环境的快速启动：
- **位置**: `/start.sh`
- **功能**:
  - 自动检测并安装依赖工具（bun、uv）
  - 准备 Python 环境
  - 安装前端依赖
  - 启动后端和前端服务
  - 支持部分启动选项

#### 使用方法
```bash
# 启动所有服务
./start.sh

# 仅启动后端
./start.sh --no-frontend

# 仅启动前端
./start.sh --no-backend

# 显示帮助
./start.sh --help
```

#### 特性
- **智能工具安装**:
  - macOS: 使用 Homebrew 自动安装
  - Linux: 下载官方安装脚本
- **环境变量**: 自动从以下位置加载：
  - macOS: `~/Library/Application Support/ValueCell/.env`
  - Linux: `~/.config/valuecell/.env`
  - Windows: `%APPDATA%\ValueCell\.env`
- **调试模式**: 自动设置 `AGENT_DEBUG_MODE=true`

### 2. Python 环境准备 (prepare_envs.sh)
Python 环境初始化脚本：
- **位置**: `/python/scripts/prepare_envs.sh`
- **功能**:
  - 创建 Python 虚拟环境（Python 3.12）
  - 安装项目依赖
  - 安装 Playwright 浏览器依赖

#### 使用方法
```bash
# 从项目根目录运行
bash python/scripts/prepare_envs.sh

# 从 python 目录运行
bash scripts/prepare_envs.sh
```

### 3. 构建前清理 (pre-build.sh)
打包前的清理脚本：
- **位置**: `/frontend/scripts/pre-build.sh`
- **功能**:
  - 清理所有 `.venv` 目录
  - 删除 `__pycache__` 目录
  - 清理 `.pyc` 文件
  - 删除 `.pytest_cache`

#### 使用场景
- Tauri 应用打包前执行
- CI/CD 流程中的清理步骤
- 减小打包体积

## 脚本设计原则

### 错误处理
- 使用 `set -Eeuo pipefail` 严格模式
- 提供详细的错误信息
- 优雅的退出处理

### 跨平台兼容
- 检测操作系统类型
- 针对不同平台使用相应的包管理器
- 统一的脚本接口

### 可读性
- 彩色输出高亮重要信息
- 清晰的日志级别（INFO、WARN、ERROR）
- 进度提示

## 环境变量

### 开发环境配置
- `AGENT_DEBUG_MODE`: 启用调试模式（默认: true）
- `API_HOST`: 后端服务地址（默认: localhost）
- `API_PORT`: 后端服务端口（默认: 8000）

### 构建配置
- `TAURI_ENV_PLATFORM`: 目标平台
- `TAURI_ENV_ARCH`: 目标架构

## 最佳实践

### 1. 开发流程
```bash
# 首次设置
git clone <repo>
cd valuecell
./start.sh  # 自动处理所有设置
```

### 2. 持续集成
```yaml
# .github/workflows 中的典型用法
- name: Setup Python
  run: bash python/scripts/prepare_envs.sh

- name: Build Frontend
  run: |
    bash frontend/scripts/pre-build.sh
    cd frontend && bun run build
```

### 3. 生产部署
```bash
# 清理并构建
bash frontend/scripts/pre-build.sh
bun run tauri build
```

## 故障排除

### 常见问题

1. **权限错误**
   ```bash
   chmod +x start.sh
   chmod +x python/scripts/prepare_envs.sh
   ```

2. **工具未找到**
   - 确保在正确的目录运行脚本
   - 检查 PATH 环境变量

3. **Python 版本问题**
   - 确保安装了 Python 3.12+
   - 使用 `pyenv` 管理多版本

4. **网络问题**
   - 检查代理设置
   - 使用镜像源（中国用户）

## 相关文件清单

### 根目录脚本
- `start.sh`: 主启动脚本

### Python 脚本
- `python/scripts/prepare_envs.sh`: Python 环境准备

### 前端脚本
- `frontend/scripts/pre-build.sh`: 构建前清理

## 扩展指南

### 添加新脚本
1. 在相应目录创建脚本文件
2. 添加执行权限
3. 遵循现有命名和风格约定
4. 更新此文档

### 脚本模板
```bash
#!/bin/bash
set -Eeuo pipefail

# 颜色定义
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m'

# 日志函数
info() { echo -e "${BLUE}[INFO]${NC}  $*"; }
success() { echo -e "${GREEN}[ OK ]${NC}  $*"; }
warn() { echo -e "${YELLOW}[WARN]${NC}  $*"; }
error() { echo -e "${RED}[ERR ]${NC}  $*" 1>&2; }

# 主要逻辑
main() {
    info "Starting script..."
    # Your code here
    success "Done!"
}

main "$@"
```

## 变更记录 (Changelog)
- 2025-12-05: 初始文档创建，整理所有构建脚本