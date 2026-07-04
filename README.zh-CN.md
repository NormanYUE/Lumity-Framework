# Lumity Framework

[English](README.md)

一个面向 Unity 2022.3 或更新版本的 DLL-only Unity 框架包。Lumity 提供模块化的 Manager 系统，包含事件驱动架构、对象池化、资源管理、UI 管理、存档系统和定时器功能。

## 安装

通过 Unity Package Manager 添加：

```
https://github.com/NormanYUE/Lumity-Framework.git#develop
```

## 快速开始

### 1. 设置 Bootstrap

在首场景中添加 `LumityBootstrap`。这会创建所有内置 Manager，并通过全局 `Lumity` 门面访问它们。

```csharp
// Manager 由 LumityBootstrap 自动创建
// 通过静态属性访问：
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

### 2. 自定义 Manager

继承 `ManagerBase` 创建自定义 Manager：

```csharp
public class AudioManager : ManagerBase
{
    protected override void OnManagerInit()
    {
        // 初始化你的 Manager
    }

    protected override void OnManagerShutdown()
    {
        // 清理资源
    }
}

// 在启动时注册
Lumity.EnsureManager<AudioManager>();

// 稍后访问
var audio = Lumity.Get<AudioManager>();
```

## 核心系统

### 事件系统

基于字符串的事件系统，支持类型安全的载荷：

```csharp
// 订阅事件
var sub = Lumity.Event.Subscribe("PlayerHit", () => Debug.Log("被击中！"));
var sub2 = Lumity.Event.Subscribe<int>("Damage", dmg => HP -= dmg);

// 发布事件
Lumity.Event.Publish("PlayerHit");
Lumity.Event.Publish("Damage", 25);

// 队列事件（下一帧分发）
Lumity.Event.Post("DelayedEvent");
Lumity.Event.Flush(); // 手动刷新

// 取消订阅
sub.Dispose(); // IDisposable 模式
Lumity.Event.Unsubscribe("PlayerHit", handler);
```

### 对象池化

两种池类型：ClassPool 用于纯 C# 对象，GameObjectPool 用于 Unity 对象：

```csharp
// Class Pool
var pool = Lumity.ObjectPool.GetClassPool<MyItem>();
var item = pool.Rent();
pool.Release(item);

// 或使用快捷方式
var item2 = Lumity.ObjectPool.Rent<MyItem>();
Lumity.ObjectPool.Release(item2);

// 实现 IPoolable 接收生命周期回调
public class MyItem : IPoolable
{
    public void OnRent() { /* 重置状态 */ }
    public void OnReturn() { /* 清理 */ }
}

// GameObject Pool
var goPool = Lumity.ObjectPool.GetGameObjectPool("Prefabs/Bullet");
goPool.WarmUp(100); // 预创建实例

var bullet = goPool.Rent(position, rotation);
goPool.Release(bullet);
```

### 资源管理

基于 Addressables 的资源加载，自动生命周期管理：

```csharp
// 异步加载
var handle = Lumity.Resource.LoadAsync<GameObject>("Prefabs/Enemy");
// 检查状态
if (handle.Status == ResourceStatus.Loaded)
{
    var prefab = handle.Asset;
}

// 同步加载
var handle2 = Lumity.Resource.LoadSync<GameObject>("Prefabs/Enemy");

// 按标签批量加载
var handles = Lumity.Resource.LoadAssets<GameObject>("Enemies");

// 绑定到 GameObject（销毁时自动释放）
handle.BindTo(gameObject);

// 手动释放
Lumity.Resource.Release(handle);

// 超时支持
var handle3 = Lumity.Resource.LoadAsync<GameObject>("Address", timeoutSeconds: 5f);
```

### 场景管理

集成 Addressables 的场景加载：

```csharp
// 单场景模式
Lumity.Scene.LoadScene("Scenes/MainMenu");
await Lumity.Scene.LoadSceneAsync("Scenes/Game");

// 多场景模式
Lumity.Scene.AddScene("Scenes/UI");
Lumity.Scene.AddScene("Scenes/Environment");
await Lumity.Scene.AddSceneAsync("Scenes/Props");
Lumity.Scene.RemoveScene("Scenes/UI");

// 查询
string active = Lumity.Scene.ActiveScene;
var loaded = Lumity.Scene.LoadedScenes;
bool loading = Lumity.Scene.IsLoading;

// 事件
Lumity.Event.Subscribe<SceneLoadedEvent>("SceneLoaded", e => Debug.Log(e.Address));
```

### UI 管理

完整的 UI 面板系统，支持 Panel/Popup/Toast 类型：

```csharp
// 设置 UIConfig（ScriptableObject）
Lumity.UI.SetConfig(uiConfig);

// 显示面板（自动根据配置识别类型）
Lumity.UI.Show(UIPanelId.MainMenu);      // Panel 类型
Lumity.UI.Show(UIPanelId.Settings);      // Popup 类型
Lumity.UI.Show(UIPanelId.GameSaved);     // Toast 类型

// 异步加载
await Lumity.UI.ShowAsync(UIPanelId.HeavyPanel);

// 隐藏/关闭
Lumity.UI.Hide(UIPanelId.MainMenu);      // 隐藏（可重新显示）
Lumity.UI.Close(UIPanelId.Settings);     // 销毁

// 弹窗栈
Lumity.UI.HideTopPopup();
Lumity.UI.HideAllPopups();

// 查询
bool visible = Lumity.UI.IsVisible(UIPanelId.MainMenu);
bool anyVisible = Lumity.UI.IsAnyVisible(UIPanelId.Settings, UIPanelId.PauseMenu);

// 事件
Lumity.Event.Subscribe<UIPanelShownEvent>("UIPanelShown", e => Debug.Log(e.Id));
```

#### UIPanel 基类

```csharp
public class MainMenuPanel : UIPanel
{
    protected override void OnShow()
    {
        // 面板变为可见
    }

    protected override void OnHide()
    {
        // 面板隐藏
    }

    protected override void OnClose()
    {
        // 面板即将销毁
    }
}
```

### 存档系统

基于 JSON 的存档/读档，支持生命周期回调：

```csharp
// 定义存档数据
[Serializable]
public class GameSaveData : SaveDataBase
{
    public int Level;
    public int Score;
    public string PlayerName;

    protected override void OnInitialize()
    {
        // 首次加载默认值
        Level = 1;
        Score = 0;
        PlayerName = "Player";
    }

    protected override void OnBeforeSave()
    {
        // 保存前逻辑
    }

    protected override void OnAfterLoad()
    {
        // 加载后逻辑
    }
}

// 保存
var data = new GameSaveData { Level = 5, Score = 100 };
Lumity.Save.Save(data, "slot1.sav");

// 加载（不存在时自动初始化）
var loaded = Lumity.Save.Load<GameSaveData>("slot1.sav");

// 查询
bool exists = Lumity.Save.Exists("slot1.sav");
Lumity.Save.Delete("slot1.sav");

// 自动保存
Lumity.Save.EnableAutoSave(30f); // 每30秒
Lumity.Save.DisableAutoSave();

// 自定义存档目录
Lumity.Save.SaveDirectory = "/custom/path";

// 事件
Lumity.Event.Subscribe<SaveCompletedEvent>("SaveCompleted", e => 
{
    if (e.Success) Debug.Log($"已保存 {e.FileName}");
});
```

### 定时器系统

延迟和重复调用，支持生命周期绑定：

```csharp
// 延迟
var handle = Lumity.Timer.Delay(5f, () => Debug.Log("5秒后"));

// 重复（无限）
var handle2 = Lumity.Timer.Repeat(1f, () => Debug.Log("每秒"));

// 重复（指定次数）
var handle3 = Lumity.Timer.Repeat(1f, () => Debug.Log("重复"), 10);

// 延迟后重复
var handle4 = Lumity.Timer.DelayRepeat(2f, 0.5f, () => Debug.Log("延迟重复"));

// 帧延迟
var handle5 = Lumity.Timer.DelayFrame(3, () => Debug.Log("3帧后"));

// 场景绑定（场景切换时自动取消）
Lumity.Timer.Delay(10f, () => {}, bindToScene: true);
Lumity.Timer.Repeat(1f, () => {}, -1, bindToScene: true);

// 池化对象绑定（对象失效时自动取消）
var bullet = goPool.Rent();
Lumity.Timer.Delay(3f, () => goPool.Release(bullet), bullet);

// 控制
handle.Pause();
handle.Resume();
handle.Cancel();

// 状态
bool active = handle.IsActive;
float elapsed = handle.Elapsed;
float remaining = handle.Remaining;
float progress = handle.Progress; // 0-1

// IDisposable 支持
using (Lumity.Timer.Delay(5f, () => {}))
{
    // 作用域结束时自动取消
}

// 全局控制
Lumity.Timer.CancelAll();
Lumity.Timer.PauseAll();
Lumity.Timer.ResumeAll();
Lumity.Timer.TimeScale = 0.5f; // 半速
```

### 配置表

类型安全的配置数据访问：

```csharp
// 定义配置行
public struct EnemyConfig
{
    public int Id;
    public string Name;
    public int HP;
    public float Speed;
}

// 创建并注册表
var table = new ConfigTable<EnemyConfig, int>(
    row => row.Id,
    new[]
    {
        new EnemyConfig { Id = 1, Name = "Slime", HP = 100, Speed = 2f },
        new EnemyConfig { Id = 2, Name = "Goblin", HP = 150, Speed = 3f },
    }
);
Lumity.Config.RegisterTable(table);

// 查询
var enemy = Lumity.Config.Get<EnemyConfig>(1);
if (Lumity.Config.TryGet<EnemyConfig>(2, out var goblin))
{
    Debug.Log(goblin.Name);
}
```

## 架构

### Manager 生命周期

所有 Manager 遵循以下生命周期：
1. `Awake()` - 自动注册到 Bootstrap
2. `OnManagerInit()` - Bootstrap 初始化时调用
3. `Update()` - 每帧逻辑（如果需要）
4. `OnManagerShutdown()` - Bootstrap 销毁时调用
5. `OnDestroy()` - 清理并发出警告

### 门面生成

对于 HybridCLR 热更新项目，生成项目侧门面：

```csharp
// 生成的代码
public static class GameLumity
{
    public static void Initialize() { /* ... */ }
    public static AudioManager Audio => Lumity.Get<AudioManager>();
    public static NetworkManager Network => Lumity.Get<NetworkManager>();
}

// 使用
GameLumity.Initialize();
GameLumity.Audio.PlaySound("click");
```

## 要求

- Unity 2022.3 或更新版本
- .NET Standard 2.1
- C# 10
- Addressables 包（可选，用于 Resource/Scene Manager）

## 许可证

MIT

## 链接

- [源码仓库](https://github.com/NormanYUE/Lumity-Framework-Private)
- [包仓库](https://github.com/NormanYUE/Lumity-Framework)
