# Changelog

[中文](CHANGELOG.zh-CN.md)

## 0.3.0

- Add `ResourceManager` with Addressables integration for async/sync resource loading.
- Add `ResourceHandle<T>` for type-safe resource lifecycle management with ref counting.
- Add `GameObjectPool` for Prefab instance pooling with warmup support.
- Add `ConfigTable<T,TKey>` for type-safe configuration data access.
- Improve `EventManager` performance by replacing `DynamicInvoke` with typed delegates.
- Fix `EventManager.Flush` infinite loop risk by snapshotting queue before dispatch.
- Fix `ResourceManager.LoadAssets` and `LoadAssetsAsync` missing operation status checks.

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
