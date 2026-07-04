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
Lumity.Scene
```

Version 0.5.0 adds UIManager with complete panel management system, supporting Panel/Popup/Toast types with configuration-driven approach.

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

## Scene Manager

Use `SceneManager` for scene loading and management:

```csharp
// Single scene mode
Lumity.Scene.LoadScene("Scenes/MainMenu");
await Lumity.Scene.LoadSceneAsync("Scenes/Game");

// Multi scene mode
Lumity.Scene.AddScene("Scenes/UI");
Lumity.Scene.AddScene("Scenes/Environment");
Lumity.Scene.RemoveScene("Scenes/UI");

// Query
string active = Lumity.Scene.ActiveScene;
var loaded = Lumity.Scene.LoadedScenes;
bool loading = Lumity.Scene.IsLoading;
```

## UI Manager

Use `UIManager` for complete UI panel management:

```csharp
// Configure UI (setup once)
Lumity.UI.SetConfig(uiConfig);

// Show panels (auto-detect type from config)
Lumity.UI.Show(UIPanelId.MainMenu);      // Panel type
Lumity.UI.Show(UIPanelId.Settings);      // Popup type
Lumity.UI.Show(UIPanelId.GameSaved);     // Toast type

// Hide/Close
Lumity.UI.Hide(UIPanelId.MainMenu);
Lumity.UI.Close(UIPanelId.Settings);

// Popup stack
Lumity.UI.HideTopPopup();
Lumity.UI.HideAllPopups();

// Query
bool visible = Lumity.UI.IsVisible(UIPanelId.MainMenu);
bool anyVisible = Lumity.UI.IsAnyVisible(UIPanelId.Settings, UIPanelId.PauseMenu);
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
