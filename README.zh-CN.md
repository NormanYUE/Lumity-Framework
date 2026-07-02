# Lumity Framework

[English](README.md)

Lumity Framework 是一个面向 Unity 2022.3.62f2 或更新版本的 DLL-only Unity 包。

## 使用方式

在首场景中添加 `LumityBootstrap`。内置 Manager 会注册到 Bootstrap 下，并可以通过全局 `Lumity` 门面访问：

```csharp
Lumity.Event
Lumity.ObjectPool
Lumity.Config
Lumity.Fsm
Lumity.UI
Lumity.Resource
```

0.1.0 版本提供 Manager 启动基础和内置 Manager 骨架。完整的 Manager 功能会在后续版本逐步加入。
