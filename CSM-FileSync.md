# `CSM-FileSync` — CSM 模块接口文档

---

## 功能简述

`CSM-FileSync` 是一个 CSM 模块，用于将本地的数据文件备份到网络服务器中。

本模块是文件同步的后台引擎，持续监控本地数据文件夹，将新产生的文件按原始目录结构上传到远程服务器。支持 `文件拷贝(针对NAS)` 和 `FTP协议`，其他协议可以通过继承 Protocol.lvclass 实现拓展。未完成的任务会被持久化，程序重启后自动续传。

---

## 模块信息

| 属性           | 值                 |
| -------------- | ------------------ |
| LabVIEW 版本   | ≥ 2020             |
| 支持的操作系统 | Windows / Linux    |
| 支持 RT        | ✅ 支持            |
| 支持 64-bit    | ✅ 支持            |
| 所属模块组     | CSM-FileSync.lvlib |

---

## 依赖项

| 依赖                                                                                    | 类型 |
| --------------------------------------------------------------------------------------- | ---- |
| [Communicable-State-Machine](https://github.com/NEVSTOP-LAB/Communicable-State-Machine) | 必须 |
| NEVTOP-Programming-Palette                                                              | 必须 |
| MGI                                                                                     | 必须 |

---

## API 接口（消息接口）

以下是外部调用者可以发送给本模块的消息。

### `API: Start`

启动文件同步服务，开始监控源文件夹并上传文件到服务器。

- **参数**：N/A
- **响应**：N/A

### `API: Stop`

停止文件同步服务。

- **参数**：N/A
- **响应**：N/A

### 参数类型说明

| 类型        | 说明                                                                                              |
| ----------- | ------------------------------------------------------------------------------------------------- |
| `HexStr`    | 将 LabVIEW Variant 序列化为十六进制字符串，内置支持                                               |
| `SafeStr`   | 将特殊字符编码为 `%[HEXCODE]`，内置支持                                                           |
| `ErrStr`    | 将错误信息编码为字符串，内置支持                                                                  |
| `APIString` | 支持嵌套键值对的纯文本字符串，需要 CSM API String Arguments Support 插件                          |
| `MassData`  | 内存映射缓冲区，传递 `Start:N,Size:M`，需要 CSM MassData Parameter Support 插件                   |
| 用户自定义  | 由模块自行解析的字符串，无需额外插件，但是要说明具体的解析规则和格式                              |

> **注意**：接口文档中对 `String` 类型数据统一使用 `APIString` 标注（不直接写 `SafeStr`），因为 `SafeStr` 正是 `APIString` 针对 `String` 类型的内部实现。

---

## 状态广播接口

以下是本模块**发出**的消息，用于通知订阅者内部状态变化。

### `Status Change`

**默认广播类型**：`Status`

同步引擎连接状态发生变化时广播。

- **参数**：用户自定义 — `String`：状态描述字符串

### `Uploading List Change`

**默认广播类型**：`Status`

待上传文件队列发生变化时广播。

- **参数**：用户自定义 — `String`：待上传文件列表

---

## 配置说明

### External API VI 配置

可以通过 `CSM-FileSync.lvlib` 中的 External API VI 进行配置：

| External API VI       | 说明                                                                |
| --------------------- | ------------------------------------------------------------------- |
| `Config FTPSync.vi`   | 配置 FTP 协议同步参数（服务器地址、账号、端口、源路径、目标路径等） |
| `Config LocalSync.vi` | 配置本地文件拷贝/NAS 协议同步参数（源路径、目标路径等）             |

---

## 调用限制与注意事项

> [!IMPORTANT]
>
> - 本模块可以在无 UI 的情况下独立运行。
> - 未完成的任务会被持久化，程序重启后自动续传。
> - 支持通过继承 Protocol.lvclass 拓展其他协议（如 webDAV 等）。
> - 可配合 `CSM-FileSyncWindow` 模块展示同步状态（另见 `CSM-FileSyncWindow.md`）。

---

## 使用示例

> 将 `CSM-FileSync` 替换为启动模块 VI 时实际使用的名称。

### 基本生命周期

```csm
// 启动文件同步服务
API: Start -> CSM-FileSync

// ... 文件同步正在运行 ...

// 停止文件同步服务
API: Stop -> CSM-FileSync
```

### 配置 FTP 同步

使用 `Config FTPSync.vi` 配置 FTP 协议同步参数后启动服务：

```csm
API: Start -> CSM-FileSync
```

### 配置本地/NAS 同步

使用 `Config LocalSync.vi` 配置本地文件拷贝/NAS 协议同步参数后启动服务：

```csm
API: Start -> CSM-FileSync
```

### 订阅状态广播

```csm
// 将 CSM-FileSync 的状态路由到处理模块
Status Change@CSM-FileSync >> API: OnStatusChange@[处理模块] -><register>
Uploading List Change@CSM-FileSync >> API: OnListChange@[处理模块] -><register>

// 取消订阅
Status Change@CSM-FileSync >> API: OnStatusChange@[处理模块] -><unregister>
```

---

## 备注

- 本模块是文件同步的后台引擎，可以在无 UI 的情况下独立运行
- 支持 FTP 协议和文件拷贝/NAS 协议，可通过继承 Protocol.lvclass 拓展其他协议
- 监控文件夹目录结构会保存到服务器，支持续传功能
- 可配合 `CSM-FileSyncWindow` 模块（另见 `CSM-FileSyncWindow.md`）通过状态订阅机制展示同步状态

---

- _完整 CSM 语法参考：<https://github.com/NEVSTOP-LAB/Communicable-State-Machine/blob/main/.doc/Syntax.md>_
- _CSM Wiki：<https://nevstop-lab.github.io/CSM-Wiki/>_
