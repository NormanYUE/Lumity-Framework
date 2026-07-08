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
Lumity.Blackboard   // BlackboardManager
Lumity.BT           // BehaviorTreeManager
```

## 黑板系统

为 FSM 和 BT 系统提供共享的类型安全键值存储：

```csharp
var bb = Lumity.Blackboard.Create("enemy_001", "Fsm", "enemy_001");
bb.Set("hp", 100);
bb.Set("target", playerTransform);
int hp = bb.Get<int>("hp");
```

## 状态机

支持分层状态机（HFSM）、事件驱动和条件驱动转换：

```csharp
var config = new FsmConfig()
    .AddState("Idle", s => {
        s.OnEnter = fsm => Debug.Log("空闲");
        s.OnUpdate = (fsm, dt) => { /* ... */ };
    })
    .AddState("Chase")
    .AddState("Attack")
    .AddTransition("Idle", "Chase", new Transition {
        Condition = bb => bb.GetOrDefault("enemyNearby", false)
    })
    .AddTransition("Chase", "Attack", new Transition {
        TriggerEvent = "InRange"
    })
    .SetInitialState("Idle");

var fsm = Lumity.Fsm.Create(config, "enemy_ai");
fsm.SetCondition("enemyNearby", true);
fsm.SendEvent("InRange");
```

## 行为树

组合节点、装饰节点和叶子节点，用于 AI 决策：

```csharp
var tree = Lumity.BT.Create("enemy_bt",
    new BtSequence(
        new BtCondition(bb => bb.GetOrDefault("hasTarget", false)),
        new BtSelector(
            new BtSequence(
                new BtCondition(bb => bb.GetOrDefault("inRange", false)),
                new BtAction(ctx => { Attack(); return BtStatus.Success; })
            ),
            new BtAction(ctx => { MoveToTarget(); return BtStatus.Running; })
        )
    )
);
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
