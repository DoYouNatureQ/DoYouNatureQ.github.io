# [DeepSeek Harness Desktop](https://github.com/DoYouNatureQ/DeepSeek-Harness-Desktop)

基于 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)(`dsh`)的原生 Windows 桌面客户端。

**整个窗口就是 DeepSeek Harness 官方页面**——会话、工具调用、审批、工作区、模型与密钥、插件配置全部使用官方 UI。
桌面客户端额外提供的能力(插件安装管理、服务进程控制、运行日志)**以插件形式并入 Harness 原生设置页**,不增加任何页面之外的界面。

> 上游 DeepSeek Harness 处于开发者预览阶段。本客户端锁定 `@deepseek-ai/dsh@0.1.5-rc.2`。

---

## 核心特性

| 位置 | 能力 |
| --- | --- |
| 官方页面(整窗) | 会话、工具调用、审批、diff、工作区、计划模式、子代理等全部官方能力 |
| 设置 → 模型 | 配置 DeepSeek API Key(write-only)、切换官方模型、自定义 API 地址 —— 官方原生设置 |
| 设置 → 插件 | 官方插件配置与运行时插件列表 |
| 设置 → **桌面工具** | 由本客户端注入:插件安装 / 启用停用 / 移除、服务进程控制、监听端口、运行日志、数据目录 |
| 窗口与加载页 | 深色 / 浅色跟随 Harness 的「外观」设置(含系统主题),标题栏同步 |

---

## 快速开始

### 1. 启动

运行 **`app\DeepSeekHarness.exe`**(或从源码构建后由 `scripts\build.ps1` 生成)。

首次启动会自动:
1. 探测内置运行时(`runtime\`)与本机 Node.js;
2. 释放并安装「桌面工具」设置页插件到 web profile;
3. 启动 `dsh web` 服务(默认 `http://127.0.0.1:3080`)并在窗口内打开官方页面。

### 2. 配置 API Key

在页面左下角进入 **设置 → 模型**,粘贴 [platform.deepseek.com](https://platform.deepseek.com/) 创建的 API Key 并应用;
在 **设置 → 模型** 或输入框上方的模型选择器中选择默认模型(`deepseek-flash` / `deepseek-v4-flash` / `deepseek-v4-pro` / `deepseek-v4-flash-vision-exp`)。

密钥写入 `~/.dsh/.credentials.yaml`,即时热加载,无需重启。

### 3. 选择工作区并对话

点击 **选择工作区** 添加项目目录并选中,然后输入任务。Agent 可以读写工作区文件、执行命令并维护计划;需要审批的操作会先询问你。

---

## 桌面工具(设置页内)

在 **设置 → 桌面工具** 中:

- **插件**:输入 npm 包名 / 精确版本 / 本地目录 / git 仓库安装;启用 / 停用 / 移除;恢复默认插件集;打开 Profile 目录。
  变更通过官方 `dsh plugin`(内部调用 pnpm)完成,重启服务后生效。
- **服务**:启动 / 停止 / 重启,监听端口(默认 3080,`0` 为系统分配),在浏览器打开。
- **日志**:实时查看 `dsh web` 进程的 stdout / stderr,支持复制与清空。
- **数据与诊断**:打开数据目录、应用日志、运行时目录;启动行为开关(自动启动、遥测、关闭最小化)。

> 与官方设置重复的能力(如运行时插件列表)一律以官方设置页为准,桌面工具不再重复提供。

---

## 目录结构

```
Deepseek Harness Desktop/
├── app/                          # 已发布的原生客户端(self-contained)
├── runtime/                      # DeepSeek Harness 运行时(npm 安装 @deepseek-ai/dsh)
├── desktop-plugin/               # 「桌面工具」设置页插件(手写客户端 bundle,无需构建)
│   ├── package.json              # dsh.client(platform: web)+ dsh.bundle.patch
│   ├── index.js                  # Host 半:空实现(仅为 Loader 行可解析)
│   ├── client.js                 # 浏览器半:注册 settings.section 并渲染工具面板
│   └── cordis.patch.yml          # 插入 desktop-tools 行
├── src/DeepSeekHarness.Desktop/  # WPF 客户端源码(C# / .NET 10)
│   ├── Models/                   # 客户端配置与模型目录
│   ├── Services/                 # 进程管理、WebView 消息桥、配置、插件安装器、主题监听
│   ├── Themes/                   # dsh 设计 token 与控件样式(加载页/回退界面使用)
│   └── Assets/                   # 应用图标(与手机 App 一致的蓝底白鲸)与官方 logo
├── scripts/
│   ├── build.ps1                 # 一键构建 + 自检
│   └── make-icon.ps1             # 由 Assets\deepseek.svg 生成多尺寸 app.ico
├── deepseek-harness/             # 上游源码(仅供查阅,不参与构建与运行)
└── README.md
```

> 构建产物 `src/DeepSeekHarness.Desktop/bin`(publish 暂存)与 `obj`(MSBuild 中间产物)
> 均为可再生成的临时文件,`app/` 才是最终交付目录;两者可随时删除以节省约 138 MB 空间。
> `scripts/*.ps1` 带 UTF-8 BOM,以确保 Windows PowerShell 5.1 下中文注释正确解析。

---

## 技术架构

```
┌────────────────────── DeepSeekHarness.exe (WPF / .NET 10) ──────────────────────┐
│  WebView2(整窗)                                                                │
│    └── DeepSeek Harness 官方页面 http://127.0.0.1:<port>/                       │
│          ├── 原生设置:通用 / 模型 / 插件 / Agent 预设                             │
│          └── 桌面工具(本客户端插件)──┐                                          │
│                                      │ WebView2 消息桥 {dsh:'desktop', req, ...} │
│  ┌───────────────────────────────────▼──────────────────────────────────────┐  │
│  │ 进程管理(启动/停止/重启、stdout/stderr)  ·  RPC 客户端(Cookie 鉴权)      │  │
│  │ 插件管理(profile manifest + dsh plugin) ·  主题监听(settings.yaml)      │  │
│  └──────────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
        node dsh web --no-open --port 3080   →   $DSH_HOME (~/.dsh)
```

- **页面即应用**:窗口只有 WebView2;加载页与错误页同样由页面渲染,颜色跟随主题。
- **桌面工具是真正的 Harness 插件**:包声明 `dsh.client.platform: web` 并导出 `./client` bundle,
  在浏览器端通过 `ctx.slots.register({ name: 'settings.section', ... })` 注册设置分区;
  安装/启用走官方 bundle 机制(`dsh.bundle.patch`),因此与官方插件生态完全一致。
- **消息桥**:页面内工具面板通过 `window.chrome.webview.postMessage` 调用原生能力
  (进程控制、pnpm 安装、日志读取),原生以 `PostWebMessageAsJson` 回包。
- **主题联动**:客户端读取 `settings.yaml` 的 `ui-theme.preference`(并监听文件与系统主题变化),
  同步 Windows 标题栏明暗与加载页配色。

---

## 自检与诊断

```powershell
# 全链路自检(运行时 → Node → 服务 → RPC → 配置写入)
app\DeepSeekHarness.exe --selftest "$env:TEMP\selftest.txt" --with-server --write-test

# 页面诊断:首次进入官方页面后执行脚本(@文件形式,支持 await)
app\DeepSeekHarness.exe --exec "@C:\path\to\script.js"
```

启动与崩溃日志:`%APPDATA%\DeepSeekHarnessDesktop\app.log`。

---

## 从源码构建

前置:Windows 10/11、[.NET SDK 10](https://dotnet.microsoft.com/)、[Node.js ≥ 22.19](https://nodejs.org/)、pnpm(`npm i -g pnpm`)、Microsoft Edge WebView2 Runtime(Win11 自带)。

```powershell
# 1. 安装运行时(若 runtime/ 不存在)
cd runtime; npm install

# 2. 生成图标(可选,已内置)
powershell -ExecutionPolicy Bypass -File scripts\make-icon.ps1

# 3. 构建客户端并自检
powershell -ExecutionPolicy Bypass -File scripts\build.ps1
```

---

## 数据与配置位置

| 路径 | 内容 |
| --- | --- |
| `%APPDATA%\DeepSeekHarnessDesktop\appsettings.json` | 客户端配置(端口、路径覆盖、启动行为) |
| `%APPDATA%\DeepSeekHarnessDesktop\desktop-plugin\` | 桌面工具插件(随应用内嵌释放,link 到 profile) |
| `%APPDATA%\DeepSeekHarnessDesktop\app.log` | 启动与崩溃日志 |
| `%LOCALAPPDATA%\DeepSeekHarnessDesktop\WebView2` | 内嵌浏览器数据 |
| `~/.dsh\settings.yaml` | Harness 用户设置(主题、默认模型、API 地址) |
| `~/.dsh\.credentials.yaml` | 凭据(API Key) |
| `~/.dsh\profiles\web\` | web profile(插件依赖与 bundle 列表) |
| `~/.dsh\sessions\` | 会话记录 |

---

## 常见问题

**启动失败:未找到运行时 / Node.js**
确认 `runtime\node_modules\@deepseek-ai\dsh\lib\bin.js` 与 Node.js ≥ 22.19 存在;
在 `%APPDATA%\DeepSeekHarnessDesktop\appsettings.json` 中可用 `runtimeDir` / `nodePath` 指定。

**端口被占用**
设置 → 桌面工具 → 服务,修改端口(或填 `0` 自动分配)后保存并重启。

**设置页里没有「桌面工具」**
1. 检查日志中是否有 `桌面工具插件安装失败`(需要 pnpm);
2. 在设置 → 桌面工具不存在时,可手动执行:
   `node runtime\node_modules\@deepseek-ai\dsh\lib\bin.js plugin --profile web add "%APPDATA%\DeepSeekHarnessDesktop\desktop-plugin"`;
3. 重启服务。

**插件安装失败**
确认 `pnpm` 可用(`npm i -g pnpm`);git 来源的插件需先在其 `profiles\web\pnpm-workspace.yaml` 的 `allowBuilds` 中允许构建脚本;详见桌面工具内的操作输出。

**主题/语言**
官方页面左下角 **设置 → 通用设置** 中调整;窗口标题栏与加载页会自动跟随。

---

## 上游与许可

- 上游项目:[deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness)(MIT License)
- 文档站:[deepseek-harness.github.io](https://deepseek-harness.github.io/deepseek-harness/)
- 应用图标由上游开源仓库中的官方 DeepSeek 鲸鱼标志生成,仅用于本客户端;请遵循上游品牌指南。
- 本客户端为独立封装,与 DeepSeek 官方无隶属关系。
