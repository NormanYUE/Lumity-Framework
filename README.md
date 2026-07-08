# Lumity Framework

[中文](README.zh-CN.md)

Lumity Framework is a DLL-only Unity package for Unity 2022.3.62f2 or newer.

## Usage

Add `LumityBootstrap` to the first scene. Built-in managers are registered under the bootstrap and can be accessed through the global `Lumity` facade:

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

## Blackboard

Shared typed key-value storage for FSM and BT systems:

```csharp
var bb = Lumity.Blackboard.Create("enemy_001", "Fsm", "enemy_001");
bb.Set("hp", 100);
bb.Set("target", playerTransform);
int hp = bb.Get<int>("hp");
```

## FSM

Hierarchical state machine with event-driven and condition-driven transitions:

```csharp
var config = new FsmConfig()
    .AddState("Idle", s => {
        s.OnEnter = fsm => Debug.Log("Idle");
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

## Behavior Tree

Composites, decorators, and leaves for AI decision-making:

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
