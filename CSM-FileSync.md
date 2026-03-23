# `CSM-FileSync` — CSM 模块接口文档

---

## 功能简述

`CSM-FileSync` 是一个基于 Communicable-State-Machine(CSM) 的文件同步模块，用于将本地的数据文件备份到网络服务器中。

本模块支持 `文件拷贝(针对NAS)` 和 `FTP协议`，其他协议可以通过继承 Protocol.lvclass 实现拓展。支持本地冗余备份、监控文件夹目录结构保存到服务器、支持续传（程序再次启动后会继续未完成任务）。

---

## 模块信息

| 属性 | 值 |
| --- | --- |
| LabVIEW 版本 | ≥ 2020 |
| 支持的操作系统 | Windows / Linux |
| 支持 RT | ✅ 支持 |
| 支持 64-bit | ✅ 支持 |

---

## 依赖项

| 依赖 | 类型 |
| --- | --- |
| [Communicable-State-Machine](https://github.com/NEVSTOP-LAB/Communicable-State-Machine) | 必须 |
| NEVTOP-Programming-Palette | 必须 |
| MGI | 必须 |

---

## API 接口（消息接口）

### FileSync 模块

FileSync 是文件同步的后台引擎模块。它持续监控本地数据文件夹，将新产生的文件按原始目录结构上传到远程服务器（支持 FTP 或文件拷贝/NAS 协议）。未完成的任务会被持久化，程序重启后自动续传。

#### `API: Start`

启动文件同步服务，开始监控源文件夹并上传文件到服务器。

- **参数**：N/A
- **响应**：N/A

#### `API: Stop`

停止文件同步服务。

- **参数**：N/A
- **响应**：N/A

### FileSyncWindow 模块

FileSyncWindow 是 FileSync 的可选 UI 模块，用于展示文件同步的实时状态。

#### `API: Link to Sync Engine`

将 FileSyncWindow 链接到指定的 FileSync 引擎，建立状态订阅关系。

- **参数**：用户自定义 — `String`：FileSync 模块名称
- **响应**：N/A

#### `API: Update List`

更新界面中的待上传文件列表显示。

- **参数**：用户自定义 — `String`：待上传文件列表
- **响应**：N/A

#### `API: Update Connected Status`

更新界面中的服务器连接状态显示。

- **参数**：用户自定义 — `String`：状态描述字符串
- **响应**：N/A

#### `API: Update Statusbar`

更新界面底部状态栏信息。

- **参数**：用户自定义 — `String`：状态栏文本
- **响应**：N/A

### 参数类型说明

| 类型 | 说明 | 链接 |
| --- | --- | --- |
| 用户自定义 | 由模块自行解析的字符串，无需额外插件 | — |

---

## 状态广播接口

以下是本模块**发出**的消息，用于通知订阅者内部状态变化。

### `Status Change`

**广播类型**：`Status`

同步引擎连接状态发生变化时广播。

- **参数**：用户自定义 — `String`：状态描述字符串

### `Uploading List Change`

**广播类型**：`Status`

待上传文件队列发生变化时广播。

- **参数**：用户自定义 — `String`：待上传文件列表

---

## 配置说明

### External API VI 配置

可以通过 `CSM-FileSync.lvlib` 中的 External API VI 进行配置：

| External API VI | 说明 |
| --- | --- |
| `Config FTPSync.vi` | 配置 FTP 协议同步参数（服务器地址、账号、端口、源路径、目标路径等） |
| `Config LocalSync.vi` | 配置本地文件拷贝/NAS 协议同步参数（源路径、目标路径等） |

---

## 调用限制与注意事项

- [ ] FileSync 可以在无 UI 的情况下独立运行
- [ ] FileSyncWindow 是可选的 UI 展示模块，需要通过 `API: Link to Sync Engine` 与 FileSync 建立连接
- [ ] 未完成的任务会被持久化，程序重启后自动续传
- [ ] 支持通过继承 Protocol.lvclass 拓展其他协议（如 webDAV 等）

---

## 使用示例

### FileSync 基本生命周期

```text
// 启动文件同步服务
API: Start -> FileSync

// ... 文件同步正在运行 ...

// 停止文件同步服务
API: Stop -> FileSync
```

### 配置 FTP 同步

使用 `Config FTPSync.vi` 配置 FTP 协议同步参数后启动服务：

```text
API: Start -> FileSync
```

### 配置本地/NAS 同步

使用 `Config LocalSync.vi` 配置本地文件拷贝/NAS 协议同步参数后启动服务：

```text
API: Start -> FileSync
```

### FileSyncWindow 与 FileSync 联动

```text
// 将 FileSyncWindow 链接到 FileSync 引擎
API: Link to Sync Engine >> FileSync -> FileSyncWindow

// FileSync 会自动广播状态到 FileSyncWindow
// FileSyncWindow 订阅 FileSync 的状态广播
Status Change@FileSync >> API: Update Connected Status@FileSyncWindow -><register>
Uploading List Change@FileSync >> API: Update List@FileSyncWindow -><register>
```

---

## 备注

- FileSync 和 FileSyncWindow 是两个独立的 CSM 模块，采用松耦合设计
- FileSync 是文件同步的后台引擎，可以在无 UI 的情况下独立运行
- FileSyncWindow 是可选的 UI 展示模块，通过 CSM 的状态订阅机制与 FileSync 连接
- 支持 FTP 协议和文件拷贝/NAS 协议，可通过继承 Protocol.lvclass 拓展其他协议
- 监控文件夹目录结构会保存到服务器，支持续传功能

---

*完整 CSM 语法参考：<https://github.com/NEVSTOP-LAB/Communicable-State-Machine/blob/main/.doc/Syntax.md>*
*CSM Wiki：<https://nevstop-lab.github.io/CSM-Wiki/>*
