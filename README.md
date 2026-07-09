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
Lumity.Web          // WebManager
Lumity.BT           // BehaviorTreeManager
Lumity.Blackboard   // BlackboardManager
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

## Blackboard

Type-safe key-value store for sharing data between systems:

```csharp
var bb = Lumity.Blackboard.Create("player");
bb.Set("hp", 100);
bb.Set("name", "Hero");

int hp = bb.Get<int>("hp");
bool found = bb.TryGet<string>("name", out var name);

// Type-safe keys
var hpKey = new BlackboardKey<int>("hp");
bb.Set(hpKey, 200);
int value = bb.Get(hpKey);
```

## FSM (Finite State Machine)

Hierarchical state machine with event-driven and condition-driven transitions:

```csharp
var config = new FsmConfig()
    .AddState("idle", s => {
        s.OnEnter = fsm => Debug.Log("Enter idle");
        s.OnUpdate = (fsm, dt) => { };
        s.OnExit = fsm => Debug.Log("Exit idle");
    })
    .AddState("run")
    .SetInitialState("idle")
    .AddTransition("idle", "run", new Transition { TriggerEvent = "go" });

var fsm = Lumity.Fsm.Create(config);
fsm.SendEvent("go");
```

## Behavior Tree

Decision-making system with composites, decorators, and leaves:

```csharp
var tree = Lumity.BT.Create("enemy_ai",
    new BtSelector(
        new BtSequence(
            new BtCondition(bb => bb.Get<int>("hp") < 20),
            new BtAction(ctx => { /* flee */ return BtStatus.Success; })
        ),
        new BtAction(ctx => { /* attack */ return BtStatus.Success; })
    )
);
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
