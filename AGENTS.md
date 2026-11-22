# AGENTS.md

# AntSword Modernization Project — Agent Instructions

## 1. 项目定位

本项目基于 AntSword 原始开源项目进行二次开发和现代化重构。

原始项目：

- Repository: https://github.com/AntSwordProject/antSword
- License: MIT
- Original architecture: Electron + Node.js + JavaScript + dhtmlx

本项目目标是将原有 Electron 桌面应用重构为基于 Tauri 2 的现代化桌面应用，并建立一套完整、可扩展、可维护的插件系统。

### 核心目标

1. 移除 Electron，使用 Tauri 2 作为唯一桌面运行框架。
2. 将原有 UI 重构为 Vue 3 + TypeScript + shadcn-vue。
3. 保留原有核心业务能力和数据兼容性。
4. 建立全新的插件管理及插件运行体系。
5. 尽可能兼容原版 AntSword 插件。
6. 实现开箱即用的完整安装包，不依赖外部 Loader 或首次联网下载 Core。
7. 支持 Windows、Linux、macOS。
8. 支持离线运行和离线安装。

这是一个架构重构项目，不是简单的 UI 换皮项目。

---

## 2. 开发原则

### 2.1 保留原有能力

在重构过程中：

- 不得随意删除原版已有功能。
- 不得因为重构困难而直接放弃原版功能。
- 不得随意修改原有数据结构和业务行为。
- 不得将尚未迁移的功能伪装成已完成。
- 不得通过静态页面或 Mock 数据代替真实功能。
- 不得仅实现 UI 而忽略后端逻辑。
- 对暂时无法迁移的功能，必须记录原因、依赖和迁移计划。

迁移前必须建立原版功能清单。

### 2.2 先理解，再修改

在修改代码之前：

1. 阅读原始项目目录结构。
2. 阅读 package.json。
3. 阅读 app.js。
4. 阅读 modules/。
5. 阅读 source/。
6. 阅读 views/front/。
7. 研究原有插件加载机制。
8. 研究原有数据存储结构。
9. 研究原有请求、编码器、数据库和文件管理模块。
10. 分析原有功能与 Electron API 的耦合程度。

禁止在不了解原有模块职责的情况下直接大规模删除或重写。

### 2.3 渐进式重构

采用分阶段迁移：

- 阶段一：架构分析。
- 阶段二：新工程基础设施。
- 阶段三：核心服务迁移。
- 阶段四：插件体系建设。
- 阶段五：旧插件兼容。
- 阶段六：UI 全面迁移。
- 阶段七：数据迁移和功能验收。
- 阶段八：跨平台构建和发布。

每个阶段必须能够独立构建、测试和验证。

不得一次性重写整个项目后再尝试修复。

---

## 3. 技术栈

### 桌面后端

- Tauri 2
- Rust
- SQLite
- Tokio（仅在需要异步任务时使用）
- Serde
- SQLx 或 rusqlite（二选一，优先考虑维护成本和迁移复杂度）

### 前端

- Vue 3
- TypeScript
- Composition API
- Vite
- pnpm
- Vue Router
- Pinia
- Tailwind CSS
- shadcn-vue
- Reka UI
- Lucide Vue Next
- Zod

### 编辑器和其他组件

根据实际需求选择：

- Monaco Editor：代码编辑器。
- xterm.js：终端 UI。
- TanStack Table：复杂数据表格。
- VueUse：通用组合式工具。
- Sonner：通知提示。
- Day.js：日期处理。

不要为了使用某个库而引入不必要的依赖。

### UI 规范

使用 shadcn-vue，不使用 React 版 shadcn/ui。

官方参考：

- https://ui.shadcn.com/
- https://www.shadcn-vue.com/
- https://www.reka-ui.com/
- https://tailwindcss.com/

UI 组件采用本地源码方式管理。

禁止直接引入另一套大型 UI 组件库来代替 shadcn-vue。

允许在确有必要时使用专门的功能组件，但必须保持统一的视觉设计。

---

## 4. 推荐项目结构

在阅读原项目后，可以根据实际情况调整，但必须保持清晰的分层。

```text
project/
├── AGENTS.md
├── README.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
├── package.json
├── pnpm-lock.yaml
├── vite.config.ts
├── components.json
│
├── docs/
│   ├── architecture/
│   ├── migration/
│   ├── plugin-system/
│   ├── compatibility/
│   └── decisions/
│
├── src/
│   ├── app/
│   │   ├── router/
│   │   ├── stores/
│   │   └── providers/
│   │
│   ├── components/
│   │   ├── ui/
│   │   ├── layout/
│   │   └── shared/
│   │
│   ├── features/
│   │   ├── dashboard/
│   │   ├── sites/
│   │   ├── files/
│   │   ├── database/
│   │   ├── terminal/
│   │   ├── encoders/
│   │   ├── plugins/
│   │   └── settings/
│   │
│   ├── legacy/
│   │   ├── adapter/
│   │   ├── bridge/
│   │   └── types/
│   │
│   ├── services/
│   ├── composables/
│   ├── types/
│   ├── lib/
│   └── styles/
│
├── src-tauri/
│   ├── src/
│   │   ├── commands/
│   │   ├── services/
│   │   ├── database/
│   │   ├── plugin/
│   │   ├── legacy/
│   │   ├── security/
│   │   └── lib.rs
│   │
│   ├── capabilities/
│   ├── migrations/
│   ├── resources/
│   └── tauri.conf.json
│
├── plugins/
│   ├── builtin/
│   └── examples/
│
├── plugin-sdk/
│   ├── api/
│   ├── types/
│   └── examples/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── e2e/
│   └── compatibility/
│
└── scripts/
```

这是目标结构，不要求一次性创建所有目录。

---

## 5. 架构设计要求

采用分层架构。

### 5.1 UI 层

负责：

- 页面展示。
- 用户交互。
- 表格和表单。
- 状态展示。
- 插件 UI 容器。
- 用户操作反馈。

UI 不直接操作系统文件、数据库或底层网络接口。

### 5.2 Service 层

负责：

- 业务逻辑。
- 数据处理。
- 请求服务。
- 插件生命周期。
- 数据迁移。
- 任务管理。

### 5.3 Rust Core 层

负责：

- 文件系统访问。
- SQLite 数据库。
- 系统能力。
- 网络服务。
- 本地任务执行。
- 插件管理。
- 进程通信。
- 权限检查。

### 5.4 IPC 层

统一管理 Tauri Commands 和 Events。

要求：

- 定义统一的请求和响应类型。
- 使用 TypeScript 类型与 Rust 类型对应。
- 统一错误码。
- 统一日志格式。
- 避免前端直接调用大量零散的底层命令。
- 对耗时任务采用事件或任务 ID 进行进度通知。

禁止将业务逻辑全部塞进 Rust Commands。

---

## 6. 插件系统设计

这是本项目的核心模块之一。

插件系统需要同时支持：

1. 新版原生插件。
2. AntSword 原生旧插件。
3. 内置插件。
4. 第三方插件。
5. 本地离线安装插件。

### 6.1 新版插件规范

设计统一的 Plugin Manifest。

每个插件必须具有：

- id
- name
- version
- description
- author
- license
- entry
- apiVersion
- minAppVersion
- permissions
- dependencies
- platforms
- category

Manifest 使用 JSON 格式，并提供 JSON Schema。

插件 ID 必须稳定且唯一。

### 6.2 插件生命周期

至少支持：

- discover
- validate
- install
- enable
- disable
- initialize
- activate
- deactivate
- uninstall
- update
- reload

插件管理界面需要显示：

- 插件名称。
- 插件版本。
- 插件作者。
- 插件来源。
- 插件状态。
- API 兼容状态。
- 权限列表。
- 依赖情况。
- 更新状态。

### 6.3 插件管理功能

参考 DSH 的插件管理理念。

必须支持：

- 本地 ZIP 安装。
- 本地目录安装。
- 在线插件源。
- 插件搜索。
- 插件分类。
- 插件启用和禁用。
- 插件更新。
- 插件卸载。
- 插件依赖检查。
- 插件版本管理。
- 插件配置管理。
- 插件日志。
- 插件错误隔离。
- 插件兼容性检查。

插件市场不是第一阶段的必要功能。

首先实现可靠的本地插件管理，再扩展远程插件源。

### 6.4 插件隔离与权限

插件不能默认拥有全部系统权限。

必须建立权限声明和授权机制。

至少区分：

- 文件读取。
- 文件写入。
- 网络访问。
- 数据库访问。
- 系统信息读取。
- 外部进程调用。
- UI 扩展。
- 应用配置访问。

权限必须可查看、可审计、可撤销。

不能将 Tauri 的所有命令暴露给插件。

对无法安全隔离的旧插件必须明确提示其信任等级，不得将进程隔离误称为完整沙箱。

---

## 7. AntSword 原生插件兼容

这是本项目的重点和技术风险最高的部分。

必须先研究原生插件真实运行机制，不能仅凭插件目录结构推测兼容性。

### 7.1 兼容目标

尽可能兼容：

- 原有插件目录结构。
- package.json。
- 原有插件元数据。
- 原有插件入口。
- 原有插件配置。
- 原有插件生命周期。
- 原有核心 API。
- 原有模块引用方式。
- 原有插件 UI 交互机制。

### 7.2 兼容层设计

建立 Legacy Plugin Compatibility Layer。

建议分为：

1. Manifest Adapter：解析旧插件元数据。
2. Module Adapter：适配原有模块引用。
3. API Adapter：映射旧 API 与新版服务。
4. Runtime Adapter：处理旧插件运行时依赖。
5. UI Adapter：处理旧插件 UI。
6. Event Adapter：适配旧事件机制。
7. Storage Adapter：适配旧插件配置和数据。

必须通过实际旧插件进行验证。

### 7.3 运行时兼容

旧插件可能依赖 Electron 和 Node.js。

Tauri 不提供完整的 Electron 运行时，因此：

- 不允许假设 require('electron') 可以直接使用。
- 不允许假设 Node.js 全局对象天然存在。
- 不允许假设旧插件可以直接运行在 Vue 页面中。
- 不允许为了兼容旧插件而重新引入 Electron 作为主应用框架。

可以评估独立 Node.js Sidecar 作为旧插件运行环境。

但必须考虑：

- 多平台 Node.js 运行时打包。
- 进程通信。
- 插件依赖管理。
- 插件生命周期。
- 进程异常恢复。
- 权限控制。
- 数据隔离。
- 离线安装。
- 升级和版本兼容。

Sidecar 不是默认答案。必须先进行可行性评估。

### 7.4 兼容性分级

为每个旧插件标记兼容等级：

- L0：能够识别和安装。
- L1：元数据和配置兼容。
- L2：核心 API 兼容。
- L3：UI 和交互兼容。
- L4：完整运行兼容。

只有通过实际测试的功能才能标记为兼容。

不能承诺所有原版插件百分之百无修改运行。

---

## 8. 数据兼容

必须保留原有蚁剑用户数据迁移能力。

重点分析：

- Shell 配置。
- HTTP 配置。
- 编码器配置。
- 插件配置。
- 用户设置。
- 历史记录。
- 缓存数据。

要求：

1. 先识别原始数据格式。
2. 建立迁移程序。
3. 迁移前自动备份。
4. 保留原始数据。
5. 迁移过程支持事务。
6. 支持迁移失败恢复。
7. 建立数据版本号。
8. 不得直接覆盖用户原始数据。

数据库迁移使用版本化 Migration。

---

## 9. UI 设计规范

采用 shadcn-vue。

设计目标：

- 专业。
- 简洁。
- 现代。
- 高信息密度。
- 适合长时间使用。
- 不做过度装饰。
- 兼顾暗色和亮色主题。

### 9.1 基本要求

- 使用统一的设计 Token。
- 使用统一的间距体系。
- 使用统一的圆角体系。
- 使用统一的颜色变量。
- 使用 Lucide 图标。
- 支持响应式布局。
- 支持暗色模式。
- 保持一致的交互反馈。
- 统一空状态、加载状态和错误状态。

### 9.2 禁止事项

- 禁止大量自定义 CSS 模拟组件库。
- 禁止每个页面使用不同的按钮样式。
- 禁止随意增加渐变和阴影。
- 禁止使用过大的圆角卡片堆砌页面。
- 禁止用 emoji 替代功能图标。
- 禁止为了视觉效果牺牲信息密度。
- 禁止仅做静态 UI。
- 禁止使用 Mock 数据冒充真实功能。

### 9.3 组件优先级

优先使用：

- Button
- Input
- Textarea
- Select
- Dialog
- AlertDialog
- DropdownMenu
- ContextMenu
- Tabs
- Tooltip
- Popover
- Command
- Table
- ScrollArea
- Resizable
- Sheet
- Sidebar
- Sonner

需要特殊功能时再开发业务组件。

---

## 10. 安全规范

本项目是用于获得授权的网站管理和安全测试的桌面工具。

所有功能仅用于用户拥有或明确获得授权的目标。

重构不得新增隐蔽控制、未授权访问、规避检测或持久化植入功能。

特别要求：

- 不得将不可信插件作为可信代码执行。
- 不得允许插件绕过用户授权。
- 不得默认授予插件无限制文件系统权限。
- 不得把密钥和密码写入普通日志。
- 不得在前端暴露敏感凭据。
- 不得将外部输入直接作为系统命令执行。
- 必须对插件包进行路径穿越检查。
- 必须校验插件包和更新包。
- 在线更新必须验证来源和完整性。
- 必须提供插件权限查看和撤销机制。

对 Tauri 使用最小权限原则，合理设置 capabilities。

---

## 11. 代码质量

### TypeScript

- 开启 strict。
- 避免 any。
- 优先使用接口和类型定义。
- 统一 API 返回类型。
- 统一错误处理。
- 组件职责单一。

### Rust

- 使用清晰的模块划分。
- 避免超大 lib.rs。
- 错误类型统一。
- 合理使用 Result。
- 对文件、数据库和 IPC 边界进行校验。
- 避免不必要的 unwrap。
- 对关键逻辑编写单元测试。

### 代码规范

- 不允许重复实现已有公共能力。
- 不允许创建没有实际用途的抽象层。
- 不允许无意义地增加依赖。
- 不允许修改与任务无关的文件。
- 不允许通过禁用 lint 或类型检查掩盖问题。

---

## 12. Git 和文档规范

保持原有 Fork 历史。

不得随意重写 Git 历史。

采用 Conventional Commits：

- feat:
- fix:
- refactor:
- docs:
- test:
- build:
- chore:
- perf:
- security:

例如：

refactor(core): migrate application runtime to Tauri 2

feat(plugins): add native plugin lifecycle management

feat(legacy): introduce AntSword plugin compatibility adapter

fix(storage): preserve legacy configuration during migration

维护：

- README.md
- CHANGELOG.md
- TODO.md
- docs/architecture/
- docs/migration/
- docs/plugin-system/
- docs/compatibility/

重要架构决策必须记录为 ADR。

---

## 13. 测试和验收

每个功能必须经过实际测试。

### 前端

- TypeScript 类型检查。
- ESLint。
- Vitest。
- 组件测试。

### 后端

- cargo check。
- cargo test。
- 数据库测试。
- IPC 测试。

### 整体

- Tauri 开发运行。
- Release 构建。
- Windows 实际运行。
- Linux 实际运行。
- 离线启动。
- 插件安装和卸载。
- 数据迁移。
- 旧插件兼容测试。

禁止只以编译通过作为完成标准。

---

## 14. AI Agent 工作方式

### 开始任务前

1. 读取 AGENTS.md。
2. 查看 Git 状态。
3. 查看当前分支。
4. 检查当前目录结构。
5. 检查 package.json 和 Cargo.toml。
6. 理解相关模块。
7. 明确任务范围和验收条件。

### 执行任务时

- 一次聚焦一个阶段。
- 优先完成可验证的最小功能。
- 不随意扩大任务范围。
- 遇到架构冲突先分析。
- 不确定旧功能行为时先查源码。
- 不要编造不存在的 API。
- 不要用假实现冒充真实实现。

### 完成任务后

必须输出：

1. 修改内容。
2. 新增文件。
3. 删除文件。
4. 技术决策。
5. 执行的测试。
6. 测试结果。
7. 尚未完成的工作。
8. 可能存在的兼容性问题。
9. 下一阶段建议。

更新 TODO.md 和 CHANGELOG.md。

### 重要原则

**先分析原项目，再设计新架构；先建立兼容边界，再迁移功能；先完成真实业务，再打磨 UI。**

不要为了追求代码看起来现代而牺牲原有功能。

不要为了快速完成而破坏原有数据和插件生态。

最终交付必须是一个完整、独立、离线可启动、开箱即用的 Tauri 桌面应用。
