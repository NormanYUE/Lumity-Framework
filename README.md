# Lumity Framework

[中文](README.zh-CN.md)

Lumity Framework is a DLL-only Unity package for Unity 2022.3.62f2 or newer.

## Usage

Add `LumityBootstrap` to the first scene. Built-in managers are registered under the bootstrap and can be accessed through the global `Lumity` facade:

```csharp
Lumity.Event
Lumity.ObjectPool
Lumity.Config
Lumity.Fsm
Lumity.UI
Lumity.Resource
```

Version 0.3.0 adds ResourceManager with Addressables integration, GameObjectPool for Prefab pooling, ConfigTable for type-safe config access, and EventManager performance improvements.

## Custom Managers

Custom managers inherit `ManagerBase`. Place them under `LumityBootstrap`, or create them during startup with:

```csharp
Lumity.EnsureManager<MyCustomManager>();
```

Built-in manager properties and generated custom manager properties use cached static fields. They are assigned during startup and do not perform a registry lookup on every access.

For HybridCLR hot update projects, generate a project-side facade such as `GameLumity` into the hot update assembly:

```csharp
GameLumity.Initialize();
GameLumity.MyCustom.DoSomething();
```

## Class Pool

Use `ClassPool<T>` for pure C# object pooling:

```csharp
var pool = new ClassPool<MyItem>();
var item = pool.Rent();
pool.Release(item);
```

Types can implement `IPoolable` to receive `OnRent()` and `OnReturn()` callbacks. `ObjectPoolManager` also exposes typed class pools:

```csharp
var item = Lumity.ObjectPool.Rent<MyItem>();
Lumity.ObjectPool.Release(item);
```

## GameObject Pool

Use `GameObjectPool` for Prefab instance pooling:

```csharp
var pool = Lumity.ObjectPool.GetGameObjectPool("Prefabs/Bullet");
pool.WarmUp(100); // Pre-create 100 instances

var bullet = pool.Rent(position, rotation);
pool.Release(bullet);
```

## Resource Manager

Use `ResourceManager` for Addressables-based resource loading:

```csharp
// Async loading
var handle = Lumity.Resource.LoadAsync<GameObject>("Prefabs/Enemy");
handle.BindTo(gameObject); // Auto-release when gameObject is destroyed

// Sync loading
var handle = Lumity.Resource.LoadSync<GameObject>("Prefabs/Enemy");

// Batch loading
var handles = Lumity.Resource.LoadAssets<GameObject>("Enemies");
```

## Config Table

Use `ConfigTable<T,TKey>` for type-safe configuration data:

```csharp
var table = new ConfigTable<EnemyConfig, int>(
    row => row.Id,
    new[] { new EnemyConfig(1, "Slime", 100) }
);
Lumity.Config.RegisterTable(table);

var enemy = Lumity.Config.Get<EnemyConfig>(1);
```
