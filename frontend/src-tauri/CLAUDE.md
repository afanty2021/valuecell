[根目录](../../CLAUDE.md) > [frontend](../) > **src-tauri**

# Tauri 桌面应用后端 (src-tauri)

## 模块职责

Tauri 模块为 ValueCell 提供 Rust 实现的桌面应用后端，负责：
- 启动和管理 Python 后端服务进程
- 提供前端与系统 API 的桥梁
- 处理应用生命周期和资源管理
- 实现跨平台的原生功能集成

## 入口与启动

### 主入口
- **启动文件**: `src/main.rs`
- **应用工厂**: `src/lib.rs` - `run()`
- **构建配置**: `build.rs`

### 初始化流程
```rust
// main.rs
fn main() {
    valuecell_lib::run()
}

// lib.rs
#[cfg_attr(mobile, tauri::mobile_entry_point)]
pub fn run() {
    tauri::Builder::default()
        .plugin(tauri_plugin_os::init())
        .setup(|app| {
            let manager = BackendManager::new(handle)?;
            manager.start_all()?;
            app.manage(manager);
            Ok(())
        })
        .build(tauri::generate_context!())
        .run(|app_handle, event| { ... });
}
```

## 对外接口

### 1. Tauri 插件集成
- **tauri-plugin-store**: 持久化存储管理
- **tauri-plugin-single-instance**: 单例应用
- **tauri-plugin-deep-link**: 深度链接处理
- **tauri-plugin-shell**: 命令执行
- **tauri-plugin-os**: 系统信息
- **tauri-plugin-log**: 日志记录
- **tauri-plugin-updater**: 自动更新

### 2. 后端进程管理
- **BackendManager**: Python 进程生命周期管理
- 优雅启动和关闭
- 进程监控和自动重启
- 日志流式传输

### 3. 系统接口
- `get_client_id()`: 获取客户端唯一标识
- 系统信息查询
- 文件系统访问
- 网络状态检测

## 关键依赖与配置

### 核心依赖 (Cargo.toml)
```toml
[dependencies]
tauri = { version = "2", features = ["devtools"] }
tauri-plugin-opener = "2"
tauri-plugin-log = "2"
tauri-plugin-shell = "2"
tauri-plugin-store = "2"
tauri-plugin-deep-link = "2"
tauri-plugin-os = "2"
tauri-plugin-single-instance = { version = "2", features = ["deep-link"] }
tauri-plugin-updater = "2"
serde = { version = "1", features = ["derive"] }
serde_json = "1"
anyhow = "1"
reqwest = { version = "0.12", features = ["blocking", "json"] }
uuid = { version = "1", features = ["v7"] }
```

### 配置文件
- **tauri.conf.json**: Tauri 应用配置
- **capabilities/default.json**: 默认权限配置
- **capabilities/desktop.json**: 桌面平台权限

## 核心组件

### 1. BackendManager (src/backend.rs)
负责 Python 后端进程的完整生命周期管理：

#### 主要功能
- **进程启动**: 使用 uv 运行 Python 服务器
- **依赖管理**: 自动安装和同步 Python 依赖
- **智能区域检测**: 自动选择镜像源（中国大陆）
- **优雅关闭**: 支持 SIGINT 和 SIGKILL 信号处理
- **日志流式传输**: 实时转发 Python 日志到文件

#### 关键实现细节
```rust
// 智能镜像源选择
fn decide_index_url() -> bool {
    // 检测地理位置，中国大陆使用阿里云镜像
}

// 优雅关闭流程
fn request_graceful_then_kill(&self, mut process: CommandChild) {
    // 1. 发送退出命令
    // 2. 等待超时时间
    // 3. 强制终止进程树
}
```

### 2. System Module (src/system.rs)
提供系统级功能和信息查询：
- 客户端 ID 生成和管理
- 系统信息获取
- 跨平台兼容性处理

### 3. 应用生命周期管理
- **窗口事件处理**: 检测窗口关闭并清理资源
- **应用退出事件**: 处理 Cmd+Q 等系统退出操作
- **单例模式**: 确保只运行一个应用实例
- **深度链接**: 支持自定义协议链接

## 平台支持

### 桌面平台
- **Windows**: 使用 taskkill 管理进程树
- **macOS**: 使用 pkill 发送 Unix 信号
- **Linux**: 支持 pkill 和标准 Unix 信号

### 移动平台 (配置)
- **Android**: 适配 Android 特性
- **iOS**: iOS 平台支持（通过 mobile_entry_point）

## 构建与打包

### 构建配置
- **build.rs**: 自定义构建脚本
- **icons/**: 多平台应用图标
- **hooks.nsh**: Windows 安装钩子

### 图标资源
- 支持 Windows (.ico)、macOS (.icns)、Linux (.png)
- 移动平台图标集
- 应用商店图标（各种尺寸）

## 性能优化

### 启动优化
- 并行初始化多个组件
- 懒加载非关键插件
- 异步依赖安装

### 内存管理
- 使用 Mutex 保护共享状态
- 及时释放进程资源
- 避免内存泄漏

## 安全特性

### 权限控制
- 最小权限原则
- 明确的能力声明
- 安全的 API 暴露

### 进程隔离
- Rust 和 Python 进程分离
- 安全的进程间通信
- 沙箱执行环境

## 调试与日志

### 日志系统
- 使用 tauri-plugin-log 统一日志
- Python 日志重定向到文件
- 多级别日志记录

### 开发工具
- Tauri devtools 集成
- 进程状态监控
- 详细的错误报告

## 常见问题 (FAQ)

### Q: Python 后端启动失败怎么办？
A: 检查 backend 目录是否存在，确保 Python 环境正确配置。

### Q: 如何添加新的 Tauri 插件？
A: 在 Cargo.toml 中添加依赖，在 lib.rs 中初始化插件。

### Q: 进程管理如何工作？
A: 使用 ShellExt 创建子进程，通过 stdin 发送命令，监控进程状态。

### Q: 如何处理跨平台差异？
A: 使用条件编译 `#[cfg(target_os)]` 处理平台特定代码。

## 相关文件清单

### 核心源码
- `src/main.rs`: 应用入口点
- `src/lib.rs`: 应用工厂和配置
- `src/backend.rs`: Python 进程管理器
- `src/system.rs`: 系统功能模块

### 配置文件
- `Cargo.toml`: Rust 项目配置
- `tauri.conf.json`: Tauri 应用配置
- `build.rs`: 构建脚本
- `capabilities/`: 权限配置

### 资源文件
- `icons/`: 应用图标（多平台）
- `hooks.nsh`: Windows 安装钩子

## 变更记录 (Changelog)
- 2025-12-05: 初始文档创建，梳理 Tauri 模块结构和功能