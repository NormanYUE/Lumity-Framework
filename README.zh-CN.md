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

0.2.0 版本提供 Manager 启动基础、内置 Manager 骨架、静态缓存访问和自定义 Manager 支持。完整的 Manager 功能会在后续版本逐步加入。

## 自定义 Manager

自定义 Manager 继承 `ManagerBase`。可以把它挂在 `LumityBootstrap` 子物体下，也可以在启动阶段通过代码创建：

```csharp
Lumity.EnsureManager<MyCustomManager>();
```

内置 Manager 属性和生成的自定义 Manager 属性都使用静态缓存字段。它们在启动阶段赋值，属性访问时不会每次查询 Registry。

对于 HybridCLR 热更新项目，可以把 `GameLumity` 这类项目侧门面生成到热更程序集：

```csharp
GameLumity.Initialize();
GameLumity.MyCustom.DoSomething();
```

## Class Pool

使用 `ClassPool<T>` 可以池化纯 C# 对象：

```csharp
var pool = new ClassPool<MyItem>();
var item = pool.Rent();
pool.Release(item);
```

类型可以实现 `IPoolable`，接收 `OnRent()` 和 `OnReturn()` 回调。`ObjectPoolManager` 也提供按类型管理的 class 池：

```csharp
var item = Lumity.ObjectPool.Rent<MyItem>();
Lumity.ObjectPool.Release(item);
```
