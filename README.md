
# UFSM - Unreal Finite State Machine Plugin

A **Blueprintable Finite State Machine Component** for Unreal Engine 4/5 created by **Bruno Xavier B. Leite**. It offers robust and extensible FSM logic that integrates seamlessly into Blueprints, Behavior Trees, Animation Blueprints, and Multiplayer networking systems.

---

## 🧠 Overview

This plugin allows game developers to construct and manipulate finite state machines (FSMs) directly within Unreal Engine. FSMs are essential for managing character AI, animation transitions, UI logic, and more.

---

## 📦 Main Component: `UStateMachineComponent`

### Purpose
An `ActorComponent` that holds a finite state machine with named states and defined transitions. It's network-ready and fully integrated with Blueprint logic.

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

## 🔗 Resources

- **Official Blog**: [UE4/5 Finite State Machines](https://brunoxavierleite.com/2016/07/03/ue4-finite-state-machine/)
