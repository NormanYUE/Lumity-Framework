# 更新日志

[English](CHANGELOG.md)

## 0.10.0

- 添加 `LogManager`，支持日志级别过滤（Verbose/Debug/Info/Warning/Error/Fatal）。
- 添加 `[LogModule]` 特性用于自定义日志输出中的模块名。
- 添加 `this.LogXxx()` 扩展方法，Manager 内部可直接调用。
- 添加 `LogManager.Xxx()` 静态 API，供非 Manager 类（BT 节点、FSM 回调）使用。
- 添加 `ILogHandler` 接口和 `LogManager` 默认输出处理器，支持可扩展输出。
- 添加递归防护和延迟格式化（GC 优化）。
- 添加时间戳到日志格式：`[HH:mm:ss.fff][LEVEL][Module] message`。
- 将框架中所有 `Debug.Log/LogWarning/LogError/LogException` 调用替换为 LogManager API。

## 0.9.0

- 为 Blackboard、FSM 和 BehaviorTree 系统添加全面的单元测试（142 个测试）。
- 添加 `CallbackActionNode` 测试辅助类，支持基于委托的行为树节点测试。

## 0.8.0

- 添加 `IQuery<T>` 接口用于类型安全的配置查询。
- 添加 `QueryCodeGenerator` 用于自动生成查询类代码。
- 添加 `CustomFileGenerator` 用于用户可扩展的查询 partial 类。
- 添加 `RowTypeScanner` 和 `ManagerScanner` 用于 Editor 时类型发现。
- 添加 `QueryExportConfig` 和 `QueryExportErrorHandler` 用于健壮的导出流水线。
- 添加 `QueryExportPanel` Editor 窗口用于可视化查询导出工作流。

## 0.7.1

- 修复 `TickCooldowns` GC 分配问题，复用静态缓冲区。
- 修复 `FsmConfig` 异常类型以保持一致的错误处理。
- 修复 `BlackboardKey<T>` 相等性和哈希码语义。
- 修复 BehaviorTreeManager `TryGet<BlackboardManager>` 模式以实现优雅降级。

## 0.7.0

- 将 `BlackboardManager` 和 `BehaviorTreeManager` 添加到 `Lumity` 静态门面。
- 在 `LumityBootstrap` 内置 Manager 中注册 `BlackboardManager` 和 `BehaviorTreeManager`。

## 0.6.0

- 添加 `Blackboard`，支持类型化键值存储和 `BlackboardKey<T>` 类型安全键。
- 添加 `BlackboardManager` 用于 Blackboard 生命周期和基于宿主的批量清理。
- 添加 `BtStatus`、`IBtNode`、`BtContext`、`BtTree` 核心行为树类型。
- 添加 `BtSequence`、`BtSelector`、`BtParallel` 组合节点。
- 添加 `BtInverter`、`BtRepeater`、`BtUntilFail`、`BtCooldown` 装饰器节点。
- 添加 `BtCondition`、`BtAction`、`BtWait` 叶子节点。
- 添加 `BehaviorTreeManager`，集成 `ClassPool` 和每棵树的 Blackboard 创建。

## 0.5.0

- 添加 `FsmConfig` 流式构建器用于 FSM 配置。
- 添加 `FsmInstance`，支持层次状态机（HFSM）、事件驱动和条件驱动转换。
- 添加 `StateNode`、`Transition`、`TransitionValidator` 核心 FSM 类型。
- 添加 `FsmManager`，集成 `ClassPool` 和每个 FSM 的 Blackboard 创建。
- 添加 `FsmStateChangedEvent` 用于 EventManager 集成。

## 0.4.0

- 添加 `TimerManager` 用于延迟和重复动作。
- 添加 `TimerHandle` 用于跟踪定时器状态（已用时间、剩余时间、进度）。

## 0.3.0

- 添加 `SaveManager` 用于游戏存档/读档系统。
- 添加 `SaveDataBase`，支持生命周期钩子（OnInitialize、OnBeforeSave、OnAfterLoad）。

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
