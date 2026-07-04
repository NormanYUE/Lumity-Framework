# 更新日志

[English](CHANGELOG.md)

## 0.7.0

- 添加 `TimerManager` 实现延迟和重复调用功能。
- 添加 `TimerHandle` 结构体，支持 `IDisposable` 实现类型安全的定时器控制。
- 添加 `Delay`/`Repeat`/`DelayRepeat`/`DelayFrame` 定时器 API。
- 添加场景绑定（场景切换时自动取消）。
- 添加池化对象绑定（对象失效时自动取消）。
- 添加 `TimeScale` 全局时间缩放。
- 添加 `Lumity.Timer` 门面属性用于访问 TimerManager。
- 修复 `DelayRepeat` 句柄现在正确取消生成的重复定时器。
- 修复 `Update` 清理安全性，使用 try/finally 处理回调异常。

## 0.6.0

- 添加 `SaveManager` 实现游戏存档系统，使用 JSON 序列化。
- 添加 `SaveDataBase` 基类，支持生命周期回调（OnInitialize/OnBeforeSave/OnAfterLoad）。
- 添加存档事件（`SaveCompleted`、`LoadCompleted`），通过 EventManager 发布。
- 添加自动保存支持，可配置间隔时间。
- 添加 `Lumity.Save` 门面属性用于访问 SaveManager。

## 0.5.0

- 重写 `UIManager`，提供完整的 UI 面板管理系统。
- 添加 `UIPanel` 基类，支持生命周期回调（OnShow/OnHide/OnClose）。
- 添加 `UIConfig` ScriptableObject 用于面板配置。
- 添加 `UIPanelId` 枚举实现类型安全的面板访问。
- 支持 Panel/Popup/Toast 三种面板类型，自动识别类型。

## 0.4.0

- 添加 `SceneManager` 实现场景加载和管理，集成 Addressables。
- 添加单场景模式（`LoadScene`/`LoadSceneAsync`）和多场景模式（`AddScene`/`RemoveScene`）。
- 添加场景事件，通过 EventManager 发布。
- 添加 `Lumity.Scene` 门面属性用于访问 SceneManager。

## 0.3.0

- 添加 `ResourceManager`，集成 Addressables 实现异步/同步资源加载。
- 添加 `ResourceHandle<T>` 实现类型安全的资源生命周期管理和引用计数。
- 添加 `GameObjectPool` 实现预制体实例池化，支持预热。
- 添加 `ConfigTable<T,TKey>` 实现类型安全的配置数据访问。
- 优化 `EventManager` 性能，用类型化委托替代 `DynamicInvoke` 反射调用。

## 0.2.0

- 添加自定义 Manager 查询和确保创建 API。
- 添加内置 Manager 的静态缓存访问。
- 添加用于自定义 Manager 缓存门面的 `GameLumity` 代码生成核心。
- 添加 `ClassPool<T>` 和 `IPoolable` 纯 C# 池化工具。
- 添加通过 `ObjectPoolManager` 访问按类型 class 池的能力。

## 0.1.0

- 添加初始 DLL-only 包结构。
- 添加 Manager 启动基础。
- 添加 Event、ObjectPool、Config、FSM、UI、Resource 内置 Manager 骨架。
