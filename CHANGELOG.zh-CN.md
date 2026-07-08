# 更新日志

[English](CHANGELOG.md)

## 0.8.0

- 添加 `BlackboardManager`，为 FSM 和 BT 系统提供共享的类型安全键值存储。
- 添加 `FsmManager`，支持分层状态机（HFSM）、事件驱动和条件驱动转换、冷却时间，以及 ClassPool 集成。
- 添加 `BehaviorTreeManager`，包含组合节点（Sequence、Selector、Parallel）、装饰节点（Inverter、Repeater、UntilFail、Cooldown）和叶子节点（Condition、Action、Wait）。
- 添加 `Lumity.Blackboard`、`Lumity.BT` 门面属性。
- 添加 `LumityBootstrap` 注册 BlackboardManager 和 BehaviorTreeManager。
- 添加 `ManagerBase.Quitting` 保护，抑制应用关闭时的误报错误日志。

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
