# UFSM - Unreal Finite State Machine Plugin

This plugin allows game developers to construct and manipulate finite state machines (FSMs) directly within Unreal Engine.  
FSMs are essential for managing character AI, animation transitions, UI logic, and more.

---

## 📦 Main Component: `UStateMachineComponent`

### Purpose
An `ActorComponent` that holds a finite state machine with named states and defined transitions.  
It's network-ready and fully integrated with Blueprint logic.

---

## ⚙️ Key Features

- Add/remove states at runtime.
- Custom state transitions with callbacks.
- State change conditions.
- Native and Blueprint Enum support.
- Built-in state timing and tracking.
- Editor integration with FSM Graphs (Editor-only).
- Network replication: Server, Client, Multicast support.
- Delegate broadcasting and Blueprint events.
- Auto transition execution and function call mapping.

---

## 🛠️ Setup Instructions

### Adding the FSM Component

1. Open your project.
2. Navigate to your Actor/Character Blueprint.
3. Click **Add Component → Custom → Finite State Machine**.
4. Set the component’s **Auto Activate** flag to ensure it ticks properly in-game.

---

## 🌀 Blueprint Integration

When added, the FSM component automatically exposes Blueprint **event hooks**:

* **OnBeginState** – called once when a state activates
* **OnUpdateState** – called every tick while a state is active
* **OnExitState** – called once when a state finishes or transitions

Each event supplies the **State ID**, **State Name**, and optionally the **elapsed Time** since activation. These are especially useful for timing logic, delayed transitions, and modular behavior.

With **Blueprint Auto‑Flow FSM** enabled (default in recent versions), you don’t need explicit calls—events fire automatically based on your defined states.

---

## 📚 Core Types

### `FSM_State`

Represents a single FSM state.

```cpp
FName Name;
UStateMachineComponent* Owner;
FSM_BeginState OnBeginState;
FSM_UpdateState OnUpdateState;
FSM_ExitState OnExitState;
```

### `FSM_Link`

Represents a link (transition) between two states.

```cpp
FName NextState;
FName Function; // Optional callback function on transition.
```

---

## 🎮 Blueprint API

### State Management

- `MakeStates(NewStates, Transition)`
- `AddState(ID, Name, Transition)`
- `RemoveState(Name, Transition)`
- `SetState(Name, Transition)`
- `SetStateID(ID, Transition)`
- `SetStateValue(Value, Transition)`

#### Conditional Set

- `SetState_Conditional(...)`
- `SetStateID_Conditional(...)`
- `SetStateValue_Conditional(...)`

### State Completion

- `FinishState(Transition)`
- `FinishState_Conditional(...)`
- With callbacks: `FinishStateWithCallbacks(...)`

---

## 🔁 Callbacks and Delegates

### Dynamic Delegates

```cpp
FSM_BPD_Initialize
FSM_BPD_BeginState
FSM_BPD_UpdateState
FSM_BPD_ExitState
```

### Self-Implementable Blueprint Events

- `Event_OnConstruct()`
- `Event_OnBeginState(...)`
- `Event_OnUpdateState(...)`
- `Event_OnExitState(...)`

---

## 🔌 Networking

Every FSM action has a counterpart for:

- **Server:** `SERVER_SetState(...)`
- **Client:** `CLIENT_SetState(...)`
- **Multicast:** `MULTI_SetState(...)`

Use these to synchronize state transitions in multiplayer scenarios.

---

## ⚙️ Advanced Configuration

### Startup State

```cpp
UPROPERTY(EditDefaultsOnly)
FName StartupState;
```

### Enum Support

```cpp
bool UseEnumFromCPP;
FString CPP_EnumName;
UEnum* K2_Enumerator;
```

### Debugging and Performance

```cpp
bool Debug;
bool AlwaysTickFSM;
bool BlueprintAutoFlowFSM;
bool BlueprintAutoFlowTransitions;
```

---

## 🧪 Pure Blueprint Functions

Use these when you need no transition callbacks:

- `Pure_AddState(ID, Name)`
- `Pure_SetState(Name)`
- `Pure_SetStateByID(ID)`
- `Pure_RemoveState(Name)`

---

## 🧩 State Information Accessors

```cpp
float GetTime();
FName GetCurrentStateName();
uint8 GetCurrentStateID();
uint8 GetPreviousStateID();
FName GetPreviousStateName();
FSM_State& GetActiveState();
bool IsInitializedFSM();
```

---

## 🔄 Transition Rule Handling

Use links and the `FinishState()` function to move between states automatically.

```cpp
TMap<FName, FSM_Link> LINKS;
```

Transitions can call a function specified in `FSM_Link.Function` when triggered.

---

## 🧰 Editor Integration

When running in the Unreal Editor, custom graph nodes (`UFSMGraph`, `UFSMGraphNode`) provide visual editing capabilities.

---

## 🧼 Utility

### Equality Operators

FSM states support `==` and `!=` comparisons based on their `Name` and `Owner`.

---

## 🧩 Integration Tips

1. **Blueprints**: Drop the component onto any `Actor`, define states, use `SetState()` or `FinishState()` nodes.
2. **AI Logic**: Combine with BTTasks (`UBTTask_FSM_SetState`, etc.) for AI FSMs.
3. **Animation**: Sync FSM states with Animation BP transitions.
4. **Enums**: Link FSM states to `UEnum` assets for scalability.
5. **Multiplayer**: Use `SERVER_`/`CLIENT_` functions to replicate state transitions.


---

## 🎬 Animation Blueprint Sync

For Skeletal Characters, you can link your FSM component to an **Animation Blueprint’s State Machine**:

* Use **Anim Blueprint** parent class *StateMachineABP*.
* In **Class Defaults**, choose:

  * **Target FSM** (your FSM Component name)
  * **Target ASM** (name of the Anim State Machine)
  * Enable **Override FSM**

This setup ensures that FSM states **auto-duplicate** your Animation State’s transitions and broadcast CLR changes to your Actor. A great way to keep animation and logic in sync.

---

## 🤖 Behavior Tree Control

Built-in **Behavior Tree Task Nodes** let you manipulate FSM states through AI logic:

* **Add State**
* **Set State**
* **Check State**
* **Remove State**

Using these, your AI Controller can dictate FSM transitions based on behavior decisions, external signals, or animation states. Multiple trees can even control different FSM subsets simultaneously.

---

## 🧠 Native C++ Usage

From C++, you can fully customize and bind logic to FSM:

```cpp
StateMachine->SetState(FName("Jump"));
StateMachine->SetState((uint8)EStates::Idle);
```

You can subscribe to state events dynamically or bind callback functions to state transitions. After version 1.3+, call `SetActive(true, false)` in your constructor to activate the FSM.

All FSM properties—including **Active State ID**, **Timing**, and **State List**—are replicated by default in multiplayer games.

---

## 🌟 Usage Example: Input & Movement FSM

1. Create a Blueprint FSM asset (e.g., **FSM\_Input**) via *Content Browser → Synaptech → FSM Component*.
2. Attach it to your Character Blueprint and mark **Auto Activate**.
3. Define states like `Normal`, `Jump`, `DoubleJump`, `Ladder`.
4. In each state’s event graph:

   * **OnBegin\_Normal**: enable input
   * **OnBegin\_Jump**: call Jump function, check if double jump
5. In Character Blueprint, forward input (axis and action) to the FSM component.
6. Ladder state logic: When inside a trigger, switch to Ladder state. Change movement mode, controls, and exit back to Normal on leave.

This yields clean, modular movement logic: **Walk/Run**, **Jump**, **Double Jump**, **Ladder climbing** — all handled via FSM transitions, with near-zero blueprint spaghetti.

---

## 📦 Full API & Integration Summary

* **State Management**: Add / Remove / Set / Finish states at runtime via Blueprint or C++
* **Event Hooks**: OnBegin, OnUpdate, OnExit for each state
* **AutoFlow** enables direct state callback execution
* **Enum support**: Use UEnum to map human-readable IDs and names
* **Tick timing**: Use elapsed state time to trigger delayed logic
* **Replication**: Full multiplayer sync of state machine properties
* **Animation/BehaviorTree sync**: Maintain consistency across logic and visuals
* **C++ integration**: Bind runtime delegates, manipulate FSM in native code

---

## 💡 Tips & Best Practices

* Use **Blueprint Enumerators** for intuitive state IDs.
* For modular input handling, isolate input logic inside an FSM component (as the blog example does).
* FSM greatly simplifies branching logic—avoid tangled if/else chains.
* Animation Blueprint sync helps unify visual states with gameplay logic.
* Use **Behavior Tree tasks** for AI-driven state transitions.
* Always set **Auto Activate** for FSM components used for ticked logic.

---

## 🧰 Included Samples

* FSM Input sample asset demonstrating movement logic (walk, jump, ladder).
* Animation Blueprint with FSM sync example.
* Behavior Tree demonstrating AI-driven transitions.
* Sample C++ classes showing native binding and FSM manipulation.

---

## ⚡ Wrap-Up

UFSM brings **structured state-driven logic** into Unreal Engine—and with the enhancements above, it's not just cleaner—it’s scalable, network-ready, animation-integrated, and AI-compatible. Use cases range from character control to gameplay state orchestration, AI management, UI flow, animations, and multiplayer systems.

User Quote:

> “With a little imagination, this little tool provides unlimited possibilities! … If you’re used to work with State Machines… you’ll quickly adapt… you can really encapsulate all code logic into Finite State rules.”

---