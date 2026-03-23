# `CSM-FileSyncWindow` — CSM 模块接口文档

---

## 功能简述

`CSM-FileSyncWindow` 是一个 CSM 模块，用于展示文件同步的实时状态。

本模块是 CSM-FileSync 的可选 UI 展示模块，通过订阅 CSM-FileSync 模块的状态广播，实时显示文件同步进度、待上传文件列表和服务器连接状态。

---

## 模块信息

| 属性 | 值 |
| --- | --- |
| LabVIEW 版本 | ≥ 2020 |
| 支持的操作系统 | Windows / Linux |
| 支持 RT | ✅ 支持 |
| 支持 64-bit | ✅ 支持 |
| 所属模块组 | CSM-FileSync.lvlib |

---

## 依赖项

| 依赖 | 类型 |
| --- | --- |
| [Communicable-State-Machine](https://github.com/NEVSTOP-LAB/Communicable-State-Machine) | 必须 |
| CSM-FileSync | 必须 |
| NEVTOP-Programming-Palette | 必须 |
| MGI | 必须 |

---

## API 接口（消息接口）

以下是外部调用者可以发送给本模块的消息。

### `API: Link to Sync Engine`

将 FileSyncWindow 链接到指定的 FileSync 引擎，建立状态订阅关系。

- **参数**：用户自定义 — `String`：FileSync 模块名称
- **响应**：N/A

### `API: Update List`

更新界面中的待上传文件列表显示。

- **参数**：用户自定义 — `String`：待上传文件列表
- **响应**：N/A

### `API: Update Connected Status`

更新界面中的服务器连接状态显示。

- **参数**：用户自定义 — `String`：状态描述字符串
- **响应**：N/A

### `API: Update Statusbar`

更新界面底部状态栏信息。

- **参数**：用户自定义 — `String`：状态栏文本
- **响应**：N/A

### 参数类型说明

| 类型 | 说明 | 链接 |
| --- | --- | --- |
| 用户自定义 | 由模块自行解析的字符串，无需额外插件 | — |

---

## 状态广播接口

本模块不对外广播状态。

---

## 配置说明

### External API VI 配置

可以通过 `CSM-FileSync.lvlib` 中的 External API VI 进行配置：

| External API VI | 说明 |
| --- | --- |
| `Link UI.vi` | 将 FileSyncWindow 链接到 FileSync 引擎，等同于调用 `API: Link to Sync Engine` |

---

## 调用限制与注意事项

- [ ] 本模块必须与 `CSM-FileSync` 模块配合使用
- [ ] 需要通过 `API: Link to Sync Engine` 或 `Link UI.vi` 建立与 FileSync 引擎的连接
- [ ] 建立连接后会自动订阅 FileSync 的 `Status Change` 和 `Uploading List Change` 状态
- [ ] 本模块为可选的 UI 展示模块，FileSync 可以在没有本模块的情况下独立运行

---

## 使用示例

> 将 `CSM-FileSyncWindow` 和 `CSM-FileSync` 替换为启动模块 VI 时实际使用的名称。

### 基本生命周期

```text
// 启动 FileSync 引擎
API: Start -> CSM-FileSync

// 启动 FileSyncWindow 并链接到 FileSync
API: Link to Sync Engine >> CSM-FileSync -> CSM-FileSyncWindow
```

### 使用 Link UI.vi 建立连接

使用 `Link UI.vi` 将 FileSyncWindow 链接到 FileSync：

```text
// FileSync 已启动，使用 Link UI.vi 建立 UI 连接
// Link UI.vi 会自动建立状态订阅关系
```

### 手动订阅状态广播

如果需要手动建立状态订阅关系：

```text
// 将 FileSync 的状态广播路由到 FileSyncWindow
Status Change@CSM-FileSync >> API: Update Connected Status@CSM-FileSyncWindow -><register>
Uploading List Change@CSM-FileSync >> API: Update List@CSM-FileSyncWindow -><register>

// 取消订阅
Status Change@CSM-FileSync >> API: Update Connected Status@CSM-FileSyncWindow -><unregister>
Uploading List Change@CSM-FileSync >> API: Update List@CSM-FileSyncWindow -><unregister>
```

---

## 备注

- 本模块是可选的 UI 展示模块，CSM-FileSync 可以在没有本模块的情况下独立运行
- 通过 CSM 的状态订阅机制与 CSM-FileSync 建立松耦合连接
- 调用 `API: Link to Sync Engine` 或 `Link UI.vi` 后会自动订阅 FileSync 的状态广播
- 实时显示文件同步进度、待上传文件列表和服务器连接状态
- 另见 `CSM-FileSync.md` 了解 FileSync 引擎模块的详细信息

---

*完整 CSM 语法参考：<https://github.com/NEVSTOP-LAB/Communicable-State-Machine/blob/main/.doc/Syntax.md>*
*CSM Wiki：<https://nevstop-lab.github.io/CSM-Wiki/>*
