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

## 模块接口文档

- [CSM-FileSync 模块接口文档](CSM-FileSync.md) — 文件同步后台引擎模块
- [CSM-FileSyncWindow 模块接口文档](CSM-FileSyncWindow.md) — 可选的文件同步状态展示 UI 模块

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
git submodule add -b v2026.03.13 https://github.com/NEVSTOP-LAB/CSM-ModSets-FileSync.git submodules/CSM-FileSync
```

```bash
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

