# Lumity Framework

[English](README.md)

Lumity Framework 是一个面向 Unity 2022.3.62f2 或更新版本的 DLL-only Unity 包。

## 使用方式

在首场景中添加 `LumityBootstrap`。内置 Manager 会注册到 Bootstrap 下，并可以通过全局 `Lumity` 门面访问：

```csharp
Lumity.Event
Lumity.ObjectPool
Lumity.Config
Lumity.Fsm
Lumity.UI
Lumity.Resource
Lumity.Scene
Lumity.Save
Lumity.Timer
```

0.7.0 版本新增 TimerManager 实现延迟和重复调用功能，支持场景绑定和池化对象绑定。

## 自定义 Manager

自定义 Manager 继承 `ManagerBase`。可以把它挂在 `LumityBootstrap` 子物体下，也可以在启动阶段通过代码创建：

```csharp
Lumity.EnsureManager<MyCustomManager>();
```

内置 Manager 属性和生成的自定义 Manager 属性都使用静态缓存字段。它们在启动阶段赋值，属性访问时不会每次查询 Registry。

对于 HybridCLR 热更新项目，可以把 `GameLumity` 这类项目侧门面生成到热更程序集：

```csharp
GameLumity.Initialize();
GameLumity.MyCustom.DoSomething();
```

## Class Pool

使用 `ClassPool<T>` 可以池化纯 C# 对象：

```csharp
var pool = new ClassPool<MyItem>();
var item = pool.Rent();
pool.Release(item);
```

类型可以实现 `IPoolable`，接收 `OnRent()` 和 `OnReturn()` 回调。`ObjectPoolManager` 也提供按类型管理的 class 池：

```csharp
var item = Lumity.ObjectPool.Rent<MyItem>();
Lumity.ObjectPool.Release(item);
```

## GameObject Pool

使用 `GameObjectPool` 池化预制体实例：

```csharp
var pool = Lumity.ObjectPool.GetGameObjectPool("Prefabs/Bullet");
pool.WarmUp(100); // 预创建 100 个实例

var bullet = pool.Rent(position, rotation);
pool.Release(bullet);
```

## Resource Manager

使用 `ResourceManager` 进行基于 Addressables 的资源加载：

```csharp
// 异步加载
var handle = Lumity.Resource.LoadAsync<GameObject>("Prefabs/Enemy");
handle.BindTo(gameObject); // gameObject 销毁时自动释放

// 同步加载
var handle = Lumity.Resource.LoadSync<GameObject>("Prefabs/Enemy");

// 批量加载
var handles = Lumity.Resource.LoadAssets<GameObject>("Enemies");
```

## Scene Manager

使用 `SceneManager` 进行场景加载和管理：

```csharp
// 单场景模式
Lumity.Scene.LoadScene("Scenes/MainMenu");
await Lumity.Scene.LoadSceneAsync("Scenes/Game");

// 多场景模式
Lumity.Scene.AddScene("Scenes/UI");
Lumity.Scene.AddScene("Scenes/Environment");
Lumity.Scene.RemoveScene("Scenes/UI");

// 查询
string active = Lumity.Scene.ActiveScene;
var loaded = Lumity.Scene.LoadedScenes;
bool loading = Lumity.Scene.IsLoading;
```

## UI Manager

使用 `UIManager` 进行完整的 UI 面板管理：

```csharp
// 配置 UI（启动时设置）
Lumity.UI.SetConfig(uiConfig);

// 显示面板（自动根据配置识别类型）
Lumity.UI.Show(UIPanelId.MainMenu);      // Panel 类型
Lumity.UI.Show(UIPanelId.Settings);      // Popup 类型
Lumity.UI.Show(UIPanelId.GameSaved);     // Toast 类型

// 隐藏/关闭
Lumity.UI.Hide(UIPanelId.MainMenu);
Lumity.UI.Close(UIPanelId.Settings);

// 弹窗栈
Lumity.UI.HideTopPopup();
Lumity.UI.HideAllPopups();

// 查询
bool visible = Lumity.UI.IsVisible(UIPanelId.MainMenu);
bool anyVisible = Lumity.UI.IsAnyVisible(UIPanelId.Settings, UIPanelId.PauseMenu);
```

## Save Manager

使用 `SaveManager` 实现游戏存档系统：

```csharp
// 定义存档数据
[Serializable]
public class GameSaveData : SaveDataBase
{
    public int Level;
    public int Score;

    protected override void OnInitialize()
    {
        Level = 1;
        Score = 0;
    }
}

// 保存
var data = new GameSaveData { Level = 5, Score = 100 };
Lumity.Save.Save(data);

// 加载（首次自动调用 OnInitialize）
var loaded = Lumity.Save.Load<GameSaveData>();

// 查询
bool exists = Lumity.Save.Exists();
Lumity.Save.Delete();

// 自动保存
Lumity.Save.EnableAutoSave(30f); // 每30秒自动保存
Lumity.Save.DisableAutoSave();
```

## Timer Manager

使用 `TimerManager` 实现延迟和重复调用：

```csharp
// 延迟 5 秒
Lumity.Timer.Delay(5f, () => Debug.Log("5 秒后"));

// 每 1 秒重复，共 10 次
Lumity.Timer.Repeat(1f, () => Debug.Log("重复"), 10);

// 延迟 2 秒后，每 0.5 秒重复
Lumity.Timer.DelayRepeat(2f, 0.5f, () => Debug.Log("延迟重复"));

// 延迟 3 帧
Lumity.Timer.DelayFrame(3, () => Debug.Log("3 帧后"));

// 场景绑定（场景切换时自动取消）
Lumity.Timer.Delay(10f, () => Debug.Log("Done"), bindToScene: true);

// 池化对象绑定（对象失效时自动取消）
var bullet = pool.Rent();
Lumity.Timer.Delay(3f, () => pool.Release(bullet), bullet);

// 控制
var handle = Lumity.Timer.Delay(10f, () => {});
handle.Pause();
handle.Resume();
handle.Cancel();

// 时间缩放
Lumity.Timer.TimeScale = 0.5f; // 半速
```

## Config Table

使用 `ConfigTable<T,TKey>` 进行类型安全的配置数据访问：

```csharp
var table = new ConfigTable<EnemyConfig, int>(
    row => row.Id,
    new[] { new EnemyConfig(1, "Slime", 100) }
);
Lumity.Config.RegisterTable(table);

var enemy = Lumity.Config.Get<EnemyConfig>(1);
```
