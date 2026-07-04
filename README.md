# Lumity Framework

[中文](README.zh-CN.md)

A DLL-only Unity framework package for Unity 2022.3 or newer. Lumity provides a modular Manager system with event-driven architecture, object pooling, resource management, UI management, save system, and timer functionality.

## Installation

Add to your Unity project via Package Manager:

```
https://github.com/NormanYUE/Lumity-Framework.git#develop
```

## Quick Start

### 1. Setup Bootstrap

Add `LumityBootstrap` to your first scene. This creates all built-in managers and makes them accessible via the global `Lumity` facade.

```csharp
// Managers are auto-created by LumityBootstrap
// Access them via static properties:
Lumity.Event      // EventManager
Lumity.ObjectPool // ObjectPoolManager
Lumity.Config     // ConfigManager
Lumity.Fsm        // FsmManager
Lumity.UI         // UIManager
Lumity.Resource   // ResourceManager
Lumity.Scene      // SceneManager
Lumity.Save       // SaveManager
Lumity.Timer      // TimerManager
```

### 2. Custom Managers

Create custom managers by inheriting `ManagerBase`:

```csharp
public class AudioManager : ManagerBase
{
    protected override void OnManagerInit()
    {
        // Initialize your manager
    }

    protected override void OnManagerShutdown()
    {
        // Cleanup resources
    }
}

// Register during startup
Lumity.EnsureManager<AudioManager>();

// Access later
var audio = Lumity.Get<AudioManager>();
```

## Core Systems

### Event System

String-based event system with type-safe payloads:

```csharp
// Subscribe to events
var sub = Lumity.Event.Subscribe("PlayerHit", () => Debug.Log("Hit!"));
var sub2 = Lumity.Event.Subscribe<int>("Damage", dmg => HP -= dmg);

// Publish events
Lumity.Event.Publish("PlayerHit");
Lumity.Event.Publish("Damage", 25);

// Queue events (dispatched next frame)
Lumity.Event.Post("DelayedEvent");
Lumity.Event.Flush(); // Manual flush

// Unsubscribe
sub.Dispose(); // IDisposable pattern
Lumity.Event.Unsubscribe("PlayerHit", handler);
```

### Object Pooling

Two pool types: ClassPool for pure C# objects, GameObjectPool for Unity objects:

```csharp
// Class Pool
var pool = Lumity.ObjectPool.GetClassPool<MyItem>();
var item = pool.Rent();
pool.Release(item);

// Or use shortcuts
var item2 = Lumity.ObjectPool.Rent<MyItem>();
Lumity.ObjectPool.Release(item2);

// Implement IPoolable for lifecycle callbacks
public class MyItem : IPoolable
{
    public void OnRent() { /* Reset state */ }
    public void OnReturn() { /* Cleanup */ }
}

// GameObject Pool
var goPool = Lumity.ObjectPool.GetGameObjectPool("Prefabs/Bullet");
goPool.WarmUp(100); // Pre-create instances

var bullet = goPool.Rent(position, rotation);
goPool.Release(bullet);
```

### Resource Management

Addressables-based resource loading with automatic lifecycle management:

```csharp
// Async loading
var handle = Lumity.Resource.LoadAsync<GameObject>("Prefabs/Enemy");
// Check status
if (handle.Status == ResourceStatus.Loaded)
{
    var prefab = handle.Asset;
}

// Sync loading
var handle2 = Lumity.Resource.LoadSync<GameObject>("Prefabs/Enemy");

// Batch loading by label
var handles = Lumity.Resource.LoadAssets<GameObject>("Enemies");

// Bind to GameObject (auto-release when destroyed)
handle.BindTo(gameObject);

// Manual release
Lumity.Resource.Release(handle);

// Timeout support
var handle3 = Lumity.Resource.LoadAsync<GameObject>("Address", timeoutSeconds: 5f);
```

### Scene Management

Scene loading with Addressables integration:

```csharp
// Single scene mode
Lumity.Scene.LoadScene("Scenes/MainMenu");
await Lumity.Scene.LoadSceneAsync("Scenes/Game");

// Multi scene mode
Lumity.Scene.AddScene("Scenes/UI");
Lumity.Scene.AddScene("Scenes/Environment");
await Lumity.Scene.AddSceneAsync("Scenes/Props");
Lumity.Scene.RemoveScene("Scenes/UI");

// Query
string active = Lumity.Scene.ActiveScene;
var loaded = Lumity.Scene.LoadedScenes;
bool loading = Lumity.Scene.IsLoading;

// Events
Lumity.Event.Subscribe<SceneLoadedEvent>("SceneLoaded", e => Debug.Log(e.Address));
```

### UI Management

Complete UI panel system with Panel/Popup/Toast types:

```csharp
// Setup UIConfig (ScriptableObject)
Lumity.UI.SetConfig(uiConfig);

// Show panels (auto-detects type from config)
Lumity.UI.Show(UIPanelId.MainMenu);      // Panel type
Lumity.UI.Show(UIPanelId.Settings);      // Popup type
Lumity.UI.Show(UIPanelId.GameSaved);     // Toast type

// Async loading
await Lumity.UI.ShowAsync(UIPanelId.HeavyPanel);

// Hide/Close
Lumity.UI.Hide(UIPanelId.MainMenu);      // Hide (can reshow)
Lumity.UI.Close(UIPanelId.Settings);     // Destroy

// Popup stack
Lumity.UI.HideTopPopup();
Lumity.UI.HideAllPopups();

// Query
bool visible = Lumity.UI.IsVisible(UIPanelId.MainMenu);
bool anyVisible = Lumity.UI.IsAnyVisible(UIPanelId.Settings, UIPanelId.PauseMenu);

// Events
Lumity.Event.Subscribe<UIPanelShownEvent>("UIPanelShown", e => Debug.Log(e.Id));
```

#### UIPanel Base Class

```csharp
public class MainMenuPanel : UIPanel
{
    protected override void OnShow()
    {
        // Panel became visible
    }

    protected override void OnHide()
    {
        // Panel hidden
    }

    protected override void OnClose()
    {
        // Panel being destroyed
    }
}
```

### Save System

JSON-based save/load with lifecycle callbacks:

```csharp
// Define save data
[Serializable]
public class GameSaveData : SaveDataBase
{
    public int Level;
    public int Score;
    public string PlayerName;

    protected override void OnInitialize()
    {
        // First load defaults
        Level = 1;
        Score = 0;
        PlayerName = "Player";
    }

    protected override void OnBeforeSave()
    {
        // Pre-save logic
    }

    protected override void OnAfterLoad()
    {
        // Post-load logic
    }
}

// Save
var data = new GameSaveData { Level = 5, Score = 100 };
Lumity.Save.Save(data, "slot1.sav");

// Load (auto-initializes if not exists)
var loaded = Lumity.Save.Load<GameSaveData>("slot1.sav");

// Query
bool exists = Lumity.Save.Exists("slot1.sav");
Lumity.Save.Delete("slot1.sav");

// Auto-save
Lumity.Save.EnableAutoSave(30f); // Every 30 seconds
Lumity.Save.DisableAutoSave();

// Custom save directory
Lumity.Save.SaveDirectory = "/custom/path";

// Events
Lumity.Event.Subscribe<SaveCompletedEvent>("SaveCompleted", e => 
{
    if (e.Success) Debug.Log($"Saved {e.FileName}");
});
```

### Timer System

Delayed and repeated actions with lifecycle binding:

```csharp
// Delay
var handle = Lumity.Timer.Delay(5f, () => Debug.Log("5 seconds later"));

// Repeat (infinite)
var handle2 = Lumity.Timer.Repeat(1f, () => Debug.Log("Every second"));

// Repeat (count)
var handle3 = Lumity.Timer.Repeat(1f, () => Debug.Log("Repeating"), 10);

// Delay then repeat
var handle4 = Lumity.Timer.DelayRepeat(2f, 0.5f, () => Debug.Log("Delayed repeat"));

// Frame delay
var handle5 = Lumity.Timer.DelayFrame(3, () => Debug.Log("3 frames later"));

// Scene binding (auto-cancel on scene change)
Lumity.Timer.Delay(10f, () => {}, bindToScene: true);
Lumity.Timer.Repeat(1f, () => {}, -1, bindToScene: true);

// Pooled object binding (auto-cancel when inactive)
var bullet = goPool.Rent();
Lumity.Timer.Delay(3f, () => goPool.Release(bullet), bullet);

// Control
handle.Pause();
handle.Resume();
handle.Cancel();

// Status
bool active = handle.IsActive;
float elapsed = handle.Elapsed;
float remaining = handle.Remaining;
float progress = handle.Progress; // 0-1

// IDisposable support
using (Lumity.Timer.Delay(5f, () => {}))
{
    // Timer auto-cancels when scope exits
}

// Global controls
Lumity.Timer.CancelAll();
Lumity.Timer.PauseAll();
Lumity.Timer.ResumeAll();
Lumity.Timer.TimeScale = 0.5f; // Half speed
```

### Config Tables

Type-safe configuration data access:

```csharp
// Define config row
public struct EnemyConfig
{
    public int Id;
    public string Name;
    public int HP;
    public float Speed;
}

// Create and register table
var table = new ConfigTable<EnemyConfig, int>(
    row => row.Id,
    new[]
    {
        new EnemyConfig { Id = 1, Name = "Slime", HP = 100, Speed = 2f },
        new EnemyConfig { Id = 2, Name = "Goblin", HP = 150, Speed = 3f },
    }
);
Lumity.Config.RegisterTable(table);

// Query
var enemy = Lumity.Config.Get<EnemyConfig>(1);
if (Lumity.Config.TryGet<EnemyConfig>(2, out var goblin))
{
    Debug.Log(goblin.Name);
}
```

## Architecture

### Manager Lifecycle

All managers follow this lifecycle:
1. `Awake()` - Auto-registers with bootstrap
2. `OnManagerInit()` - Called during bootstrap initialization
3. `Update()` - Per-frame logic (if needed)
4. `OnManagerShutdown()` - Called during bootstrap destruction
5. `OnDestroy()` - Cleanup with warning

### Facade Generation

For HybridCLR hot-update projects, generate a project-side facade:

```csharp
// Generated code
public static class GameLumity
{
    public static void Initialize() { /* ... */ }
    public static AudioManager Audio => Lumity.Get<AudioManager>();
    public static NetworkManager Network => Lumity.Get<NetworkManager>();
}

// Usage
GameLumity.Initialize();
GameLumity.Audio.PlaySound("click");
```

## Requirements

- Unity 2022.3 or newer
- .NET Standard 2.1
- C# 10
- Addressables package (optional, for Resource/Scene managers)

## License

MIT

## Links

- [Source Repository](https://github.com/NormanYUE/Lumity-Framework-Private)
- [Package Repository](https://github.com/NormanYUE/Lumity-Framework)
