# Changelog

[中文](CHANGELOG.zh-CN.md)

## 0.8.0

- Add `BlackboardManager` for shared typed key-value storage across FSM and BT systems.
- Add `FsmManager` with hierarchical state machine (HFSM), event-driven and condition-driven transitions, cooldowns, and ClassPool integration.
- Add `BehaviorTreeManager` with composites (Sequence, Selector, Parallel), decorators (Inverter, Repeater, UntilFail, Cooldown), and leaves (Condition, Action, Wait).
- Add `Lumity.Blackboard`, `Lumity.BT` facade properties.
- Add `LumityBootstrap` registration for BlackboardManager and BehaviorTreeManager.
- Add `ManagerBase.Quitting` guard to suppress false error logs during application shutdown.

## 0.2.0

- Add custom manager lookup and ensure APIs.
- Add cached static manager access for built-in managers.
- Add `GameLumity` source generation core for cached custom manager facades.
- Add `ClassPool<T>` and `IPoolable` as pure C# pooling utilities.
- Add typed class pool access through `ObjectPoolManager`.

## 0.1.0

- Add the initial DLL-only package structure.
- Add the manager bootstrap foundation.
- Add built-in manager skeletons for Event, ObjectPool, Config, FSM, UI, and Resource access.
