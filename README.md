# CSM-FileSync

## 模块功能及设计

基于 Communicable-State-Machine(CSM) 的文件同步模块。CSM FileSync 模块用于将本地的数据文件备份到网络服务器中。目前支持 `文件拷贝(针对NAS)`/`FTP协议`，其他协议可以继承 Protocol.lvclass 实现拓展。

**其他特点**：

- 支持本地冗余备份
- 监控文件夹目录结构会保存到服务器
- 支持续传，程序再次启动后会继续未完成任务
- 可通过继承拓展其他协议，如 webDAV 等

![CSM FileSync Module](_doc/CSM%20FileSync%20Module.png)

### 开发环境

- 开发版本： LabVIEW 2020
- VIPM 依赖：
  - CSM Framework v2026Q1 或以后版本
  - NEVTOP-Programming-Palette
  - MGI

## FileSync 模块接口

FileSync 是文件同步的后台引擎模块。它持续监控本地数据文件夹，将新产生的文件按原始目录结构上传到远程服务器（支持 FTP 或文件拷贝/NAS 协议）。未完成的任务会被持久化，程序重启后自动续传。FileSync 可以在无 UI 的情况下独立运行，也可配合可选的 FileSyncWindow 模块展示同步状态。

### API

| API | 描述 | 参数 | 响应 |
| --- | --- | --- | --- |
| `API: Start` | 启动文件同步服务，开始监控源文件夹并上传文件到服务器。 | N/A | N/A |
| `API: Stop` | 停止文件同步服务。 | N/A | N/A |

### 状态 (Status)

| Status | 描述 | 参数 |
| --- | --- | --- |
| `Status Change` | 同步引擎连接状态发生变化时广播。 | 状态描述字符串 |
| `Uploading List Change` | 待上传文件队列发生变化时广播。 | 待上传文件列表 |

### 配置 (Configuration)

可以通过 `CSM-FileSync.lvlib` 中的 External API VI 进行配置：

| External API VI | 描述 |
| --- | --- |
| `Config FTPSync.vi` | 配置 FTP 协议同步参数（服务器地址、账号、端口、源路径、目标路径等）。 |
| `Config LocalSync.vi` | 配置本地文件拷贝/NAS 协议同步参数（源路径、目标路径等）。 |

**示例：（假设模块名称为 "FileSync"）**

```csm
API: Start -> FileSync
API: Stop -> FileSync
```

## FileSyncWindow 模块接口

FileSyncWindow 是 FileSync 的可选 UI 模块，用于展示文件同步的实时状态。

### API

| API | 描述 | 参数 | 响应 |
| --- | --- | --- | --- |
| `API: Link to Sync Engine` | 将 FileSyncWindow 链接到指定的 FileSync 引擎，建立状态订阅关系。 | FileSync 模块名称 <br/> (类型: 普通字符串) | N/A |
| `API: Update List` | 更新界面中的待上传文件列表显示。 | 待上传文件列表 | N/A |
| `API: Update Connected Status` | 更新界面中的服务器连接状态显示。 | 状态描述字符串 | N/A |
| `API: Update Statusbar` | 更新界面底部状态栏信息。 | 状态栏文本 | N/A |

## FileSync 与 FileSyncWindow 的关系

FileSync 和 FileSyncWindow 是两个独立的 CSM 模块，采用松耦合设计：

- **FileSync** 是文件同步的后台引擎，负责实际的文件监控、队列管理和上传操作，可以在无 UI 的情况下独立运行。
- **FileSyncWindow** 是可选的 UI 展示模块，用于将 FileSync 的工作状态以界面形式呈现给用户。

两者通过 CSM 的状态订阅机制连接：调用 `Link UI.vi`（或 `API: Link to Sync Engine`）后，FileSyncWindow 会自动订阅 FileSync 发布的 `Status Change` 和 `Uploading List Change` 状态，并实时更新界面显示。

```mermaid
graph LR
    FileSync[FileSync<br/>同步引擎]
    FileSyncWindow[FileSyncWindow<br/>UI 界面]

    FileSync -->|Status Change<br/>状态订阅| FileSyncWindow
    FileSync -->|Uploading List Change<br/>状态订阅| FileSyncWindow
```

这种设计使得 FileSync 可以在不依赖任何 UI 的情况下嵌入其他应用，而 FileSyncWindow 只需通过一次 `Link UI` 调用即可接入任意 FileSync 实例。

## 下载使用

### 方法一：从 GitHub Release 下载最新版本的 PPL（lvlibp）

1. 访问 [CSM-FileSync GitHub 仓库](https://github.com/NEVSTOP-LAB/CSM-ModSets-FileSync) 的 Release 页面
2. 下载最新版本的 PPL 文件（通常命名为 `CSM-FileSync.lvlibp`）
3. 将下载的 PPL 文件放置到您的 LabVIEW 项目中合适的位置

**注意事项：**
- 确保下载的 PPL 版本与您的 LabVIEW 开发环境兼容
- PPL 文件包含已编译的代码，无法直接查看或修改源码
- 如需自定义功能，建议使用其他方法获取源码

### 方法二：使用 git submodule

通过 git submodule 将 CSM-FileSync 作为子模块添加到您的项目中：

```bash
git submodule add -b <tag> https://github.com/NEVSTOP-LAB/CSM-ModSets-FileSync.git submodules/CSM-FileSync
git submodule update --init
```

其中 `<tag>` 替换为您需要的版本标签，例如 `v2025.06.10`。

**注意事项：**
- 使用 tag 可以确保您的项目锁定到特定版本，避免因上游代码变更导致的兼容性问题
- 首次添加子模块后，需要运行 `git submodule update --init` 来同步子模块内容
- 后续更新子模块时，使用 `git submodule update --remote`

### 方法三：直接下载源码

1. 访问 [CSM-FileSync GitHub 仓库](https://github.com/NEVSTOP-LAB/CSM-ModSets-FileSync)
2. 点击 "Code" 按钮，选择 "Download ZIP"
3. 解压下载的 ZIP 文件到您的项目目录中

**注意事项：**
- 直接下载源码可以自由修改和定制功能
- 确保您的开发环境满足项目的依赖要求（见上文开发环境部分）
- 如需贡献代码，建议使用 git 克隆仓库而非直接下载

