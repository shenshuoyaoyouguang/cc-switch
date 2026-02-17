# CC Switch - AI Agent 上下文指南

## 项目概述

**CC Switch** 是一个跨平台的桌面应用程序，作为 Claude Code、Codex 和 Gemini CLI 的一站式配置管理助手。

### 核心功能
- **多供应商管理**: 支持 Claude Code、Codex、Gemini 和 OpenCode 的 API 配置切换
- **MCP 服务器管理**: 统一的 MCP 服务器配置管理面板
- **Skills 管理**: 自动发现、安装和管理 Claude Skills
- **Prompts 管理**: 多预设系统提示词管理
- **代理服务**: 内置 HTTP/SOCKS5 代理支持
- **深度链接**: 支持 `ccswitch://` 协议一键导入配置

### 技术架构

**前端栈:**
- React 18 + TypeScript
- Vite (构建工具)
- Tailwind CSS 3.4 + shadcn/ui
- TanStack Query v5 (状态管理)
- react-hook-form + zod (表单处理)
- react-i18next (国际化)

**后端栈:**
- Tauri 2.8 (Rust)
- SQLite (数据持久化)
- tokio (异步运行时)

## 项目结构

```
├── src/                      # 前端 (React + TypeScript)
│   ├── components/           # UI 组件
│   │   ├── providers/        # 供应商管理
│   │   ├── settings/         # 设置面板
│   │   ├── mcp/              # MCP 管理
│   │   ├── skills/           # Skills 管理
│   │   ├── prompts/          # Prompts 管理
│   │   ├── ui/               # shadcn/ui 基础组件
│   │   └── ...
│   ├── hooks/                # 自定义 Hooks (业务逻辑)
│   ├── lib/
│   │   ├── api/              # Tauri API 封装
│   │   └── query/            # TanStack Query 配置
│   ├── types/                # TypeScript 类型定义
│   └── i18n/                 # 国际化文件
│
├── src-tauri/                # 后端 (Rust)
│   └── src/
│       ├── commands/         # Tauri 命令层
│       ├── services/         # 业务逻辑层
│       ├── database/         # 数据库操作
│       └── ...
│
├── tests/                    # 测试文件
└── docs/                     # 项目文档
```

## 开发命令

```bash
# 安装依赖
pnpm install

# 开发模式 (热重载)
pnpm dev

# 类型检查
pnpm typecheck

# 格式化代码
pnpm format

# 运行前端单元测试
pnpm test:unit
pnpm test:unit:watch

# 构建应用
pnpm build

# Rust 后端开发
cd src-tauri
cargo fmt           # 格式化
cargo clippy        # 静态检查
cargo test          # 运行测试
```

## 关键约定

### 1. 前端架构模式
- **数据流**: UI → Hooks → React Query → API Layer → Tauri Commands
- **状态管理**: 服务端状态使用 TanStack Query，客户端 UI 状态使用 useState
- **表单处理**: 统一使用 react-hook-form + zod 进行验证

### 2. 后端架构模式
- **分层设计**: Commands → Services → DAO → Database
- **错误处理**: 统一使用 `thiserror` 定义错误类型
- **并发安全**: 使用 Mutex 保护数据库连接

### 3. 命名规范
- **前端**: PascalCase (组件), camelCase (函数/变量), UPPER_SNAKE (常量)
- **后端**: snake_case (Rust 惯例)
- **API 参数**: 统一使用 `app` 参数 (值: `claude`, `codex`, `gemini`, `opencode`)

### 4. 国际化
- 所有用户可见文本必须使用 `t()` 函数
- 翻译文件位于 `src/i18n/locales/`
- 支持语言: 中文(zh), 英文(en), 日文(ja)

## 数据存储

**SQLite 数据库**: `~/.cc-switch/cc-switch.db`
- providers (供应商配置)
- mcp_servers (MCP 服务器)
- prompts (系统提示词)
- skills (已安装技能)
- settings (应用设置)

**本地设置**: `~/.cc-switch/settings.json`
- 设备级配置 (窗口状态、本地路径等)

## 配置目录

- **Claude Code**: `~/.claude/`
- **Codex**: `~/.codex/`
- **Gemini**: `~/.gemini/`
- **OpenCode**: `~/.config/opencode/`

## 测试策略

- **前端**: Vitest + MSW + React Testing Library
- **目标**: Hooks 100% 覆盖率
- **运行**: `pnpm test:unit`

## 常见开发任务

### 添加新的供应商预设
1. 在 `src/config/` 创建/修改预设文件
2. 更新 `src/config/universalProviderPresets.ts` (如适用)
3. 添加对应图标到 `src/components/icons/`

### 添加新的 MCP 模板
1. 在 `src/config/mcpPresets.ts` 添加模板定义
2. 如有需要，更新验证 schema

### 添加新的国际化文本
1. 在 `src/i18n/locales/zh.ts` 和 `en.ts` 添加键值
2. 使用 `t('key.subkey')` 在组件中引用

## 技术债务与注意事项

1. **API 格式迁移**: v3.10.3 后将 `api_format` 从 `settings_config` 迁移到 `ProviderMeta`
2. **Windows Home 目录**: v3.10.3 修复了 HOME 环境变量与用户目录不一致的问题
3. **数据库迁移**: v3.8.0 从 JSON 文件迁移到 SQLite，保留自动迁移逻辑

## 相关文档

- `docs/REFACTORING_MASTER_PLAN.md` - 重构主计划
- `docs/TEST_DEVELOPMENT_PLAN.md` - 测试开发计划
- `CHANGELOG.md` - 版本更新日志
