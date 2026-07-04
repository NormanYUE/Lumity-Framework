# 更新日志

[English](CHANGELOG.md)

## 0.3.0

- 添加 `ResourceManager`，集成 Addressables 实现异步/同步资源加载。
- 添加 `ResourceHandle<T>` 实现类型安全的资源生命周期管理和引用计数。
- 添加 `GameObjectPool` 实现预制体实例池化，支持预热。
- 添加 `ConfigTable<T,TKey>` 实现类型安全的配置数据访问。
- 优化 `EventManager` 性能，用类型化委托替代 `DynamicInvoke` 反射调用。
- 修复 `EventManager.Flush` 无限循环风险，使用队列快照模式。
- 修复 `ResourceManager.LoadAssets` 和 `LoadAssetsAsync` 缺少操作状态检查。

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
