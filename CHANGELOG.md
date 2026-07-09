# Changelog

[中文](CHANGELOG.zh-CN.md)

## 0.9.0

- Add comprehensive unit tests for Blackboard, FSM, and BehaviorTree systems (142 tests).
- Add `CallbackActionNode` test helper for delegate-based behavior tree node testing.

## 0.8.0

- Add `IQuery<T>` interface for type-safe config queries.
- Add `QueryCodeGenerator` for automatic query class code generation.
- Add `CustomFileGenerator` for user-extensible query partial classes.
- Add `RowTypeScanner` and `ManagerScanner` for Editor-time type discovery.
- Add `QueryExportConfig` and `QueryExportErrorHandler` for robust export pipeline.
- Add `QueryExportPanel` Editor window for visual query export workflow.

## 0.7.1

- Fix `TickCooldowns` GC allocation by reusing static buffer.
- Fix `FsmConfig` exception types for consistent error handling.
- Fix `BlackboardKey<T>` equality and hash code semantics.
- Fix BehaviorTreeManager `TryGet<BlackboardManager>` pattern for graceful degradation.

## 0.7.0

- Add `BlackboardManager` and `BehaviorTreeManager` to `Lumity` static facade.
- Register `BlackboardManager` and `BehaviorTreeManager` in `LumityBootstrap` built-in managers.

## 0.6.0

- Add `Blackboard` with typed key-value storage and `BlackboardKey<T>` type-safe keys.
- Add `BlackboardManager` for blackboard lifecycle and host-based batch cleanup.
- Add `BtStatus`, `IBtNode`, `BtContext`, `BtTree` core behavior tree types.
- Add `BtSequence`, `BtSelector`, `BtParallel` composite nodes.
- Add `BtInverter`, `BtRepeater`, `BtUntilFail`, `BtCooldown` decorator nodes.
- Add `BtCondition`, `BtAction`, `BtWait` leaf nodes.
- Add `BehaviorTreeManager` with `ClassPool` integration and per-tree blackboard creation.

## 0.5.0

- Add `FsmConfig` fluent builder for FSM configuration.
- Add `FsmInstance` with hierarchical state machine (HFSM), event-driven and condition-driven transitions.
- Add `StateNode`, `Transition`, `TransitionValidator` core FSM types.
- Add `FsmManager` with `ClassPool` integration and per-FSM blackboard creation.
- Add `FsmStateChangedEvent` for EventManager integration.

## 0.4.0

- Add `TimerManager` for delayed and repeated actions.
- Add `TimerHandle` for tracking timer state (elapsed, remaining, progress).

## 0.3.0

- Add `SaveManager` for game save/load system.
- Add `SaveDataBase` with lifecycle hooks (OnInitialize, OnBeforeSave, OnAfterLoad).

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
