# Lumity Framework

[English](README.md)

Lumity Framework 是一个面向 Unity 2022.3.62f2 或更新版本的 DLL-only Unity 包。

## 使用方式

在首场景中添加 `LumityBootstrap`。内置 Manager 会注册到 Bootstrap 下，并可以通过全局 `Lumity` 门面访问：

```csharp
Lumity.Event        // EventManager
Lumity.ObjectPool   // ObjectPoolManager
Lumity.Config       // ConfigManager
Lumity.Fsm          // FsmManager
Lumity.UI           // UIManager
Lumity.Resource     // ResourceManager
Lumity.Scene        // SceneManager
Lumity.Save         // SaveManager
Lumity.Timer        // TimerManager
Lumity.Web          // WebManager
Lumity.BT           // BehaviorTreeManager
Lumity.Blackboard   // BlackboardManager
```

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

## Blackboard（黑板）

类型安全的键值存储，用于在系统间共享数据：

```csharp
var bb = Lumity.Blackboard.Create("player");
bb.Set("hp", 100);
bb.Set("name", "Hero");

int hp = bb.Get<int>("hp");
bool found = bb.TryGet<string>("name", out var name);

// 类型安全键
var hpKey = new BlackboardKey<int>("hp");
bb.Set(hpKey, 200);
int value = bb.Get(hpKey);
```

## FSM（有限状态机）

支持层次结构、事件驱动和条件驱动转换的状态机：

```csharp
var config = new FsmConfig()
    .AddState("idle", s => {
        s.OnEnter = fsm => Debug.Log("进入 idle");
        s.OnUpdate = (fsm, dt) => { };
        s.OnExit = fsm => Debug.Log("离开 idle");
    })
    .AddState("run")
    .SetInitialState("idle")
    .AddTransition("idle", "run", new Transition { TriggerEvent = "go" });

var fsm = Lumity.Fsm.Create(config);
fsm.SendEvent("go");
```

## Behavior Tree（行为树）

决策系统，支持组合节点、装饰器节点和叶子节点：

```csharp
var tree = Lumity.BT.Create("enemy_ai",
    new BtSelector(
        new BtSequence(
            new BtCondition(bb => bb.Get<int>("hp") < 20),
            new BtAction(ctx => { /* 逃跑 */ return BtStatus.Success; })
        ),
        new BtAction(ctx => { /* 攻击 */ return BtStatus.Success; })
    )
);
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
