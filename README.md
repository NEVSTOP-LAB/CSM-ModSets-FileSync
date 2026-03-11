# CSM-FileSync

## 模块功能及设计

基于 Communicable-State-Machine(CSM) 的文件同步模块。CSM FileSync 模块用于将本地的数据文件备份到网络服务器中。目前支持 `文件拷贝(针对NAS)`/`FTP协议`，其他协议可以继承 Protocol.lvclass 实现拓展。

**其他特点**：

- 支持本地冗余备份
- 监控文件夹目录结构会保存到服务器
- 支持续传，程序再次启动后会继续未完成任务
- 可通过继承拓展其他协议，如 webDAV 等

![CSM FileSync Module](_doc/CSM%20FileSync%20Module.png)

## 下载使用

- 开发版本： LabVIEW 2020
- VIPM 依赖：
  - CSM Framework v2025.May 起
  - MGI
  - Hooovahh Array VIMs v3.1.1.22 by Hooovahh

## 模块展示 & 使用截图

![image](https://github.com/user-attachments/assets/9a9cb5a6-106f-4a60-823e-7ce471767a4d)

![image](https://github.com/user-attachments/assets/ea977005-3c3b-4476-9cb1-c83e3ec27fba)

- 可以使用 CSM-FileSync.lvlib 中的 External API 调用模块
- 使用源码的情况下，可以使用 CSM API调用
- (可选) 可以将同步的状态显示到 FileSyncWindow 界面
- (拓展）如果目前的协议不能满足备份需求，仿照 FTPProtocol.lvclass, 添加新的备份协议，即可支持其他协议。欢迎有兴趣的开发者提交 PR 到该仓库扩展新的协议，如 WebDAV 等.

## 开源说明

```
请 Star 该仓库及 CSM 仓库后，可以随意应用到你的项目中。
也不强制:) 

开源代码，请谨慎判断是否满足你的要求，该代码使用带来的收益和损失，均与原作者无关。
如果收益，欢迎打赏:)
```
