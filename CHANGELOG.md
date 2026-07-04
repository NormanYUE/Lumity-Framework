# Changelog

[中文](CHANGELOG.zh-CN.md)

## 0.7.0

- Add `TimerManager` for delayed and repeated actions.
- Add `TimerHandle` struct with `IDisposable` support for type-safe timer control.
- Add `Delay`/`Repeat`/`DelayRepeat`/`DelayFrame` timer APIs.
- Add scene binding (auto-cancel on scene change).
- Add pooled object binding (auto-cancel when object inactive).
- Add `TimeScale` for global time scaling.
- Add `Lumity.Timer` facade property for TimerManager access.
- Fix `DelayRepeat` handle now correctly cancels spawned repeat timers.
- Fix `Update` cleanup safety with try/finally for callback exceptions.

## 0.6.0

- Add `SaveManager` for game save/load system with JSON serialization.
- Add `SaveDataBase` base class with lifecycle callbacks (OnInitialize/OnBeforeSave/OnAfterLoad).
- Add save/load events (`SaveCompleted`, `LoadCompleted`) via EventManager.
- Add auto-save support with configurable interval.
- Add `Lumity.Save` facade property for SaveManager access.

## 0.5.0

- Rewrite `UIManager` with complete panel management system.
- Add `UIPanel` base class with lifecycle callbacks (OnShow/OnHide/OnClose).
- Add `UIConfig` ScriptableObject for panel configuration.
- Add `UIPanelId` enum for type-safe panel access.
- Support Panel/Popup/Toast panel types with automatic type detection.

## 0.4.0

- Add `SceneManager` for scene loading and management with Addressables integration.
- Add single scene mode (`LoadScene`/`LoadSceneAsync`) and multi scene mode (`AddScene`/`RemoveScene`).
- Add scene events via EventManager.
- Add `Lumity.Scene` facade property for SceneManager access.

## 0.3.0

- Add `ResourceManager` with Addressables integration for async/sync resource loading.
- Add `ResourceHandle<T>` for type-safe resource lifecycle management with ref counting.
- Add `GameObjectPool` for Prefab instance pooling with warmup support.
- Add `ConfigTable<T,TKey>` for type-safe configuration data access.
- Improve `EventManager` performance by replacing `DynamicInvoke` with typed delegates.

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
