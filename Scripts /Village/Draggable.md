# 📚 Draggable.cs - Complete Line-by-Line Documentation

**File Path:** `Assets/Scripts/Village/Draggable.cs`

This document provides an **extremely detailed, line-by-line explanation** of the Draggable script, teaching all Unity concepts from the ground up.

---

# Script Purpose

The `Draggable` script handles **drag-and-drop building placement** in the Village scene. It:

1. Allows players to drag buildings to position them
2. Snaps buildings to a grid for neat alignment
3. Handles two modes: **Placement** (dragging) and **Placed** (click to delete)
4. Implements Unity's pointer event interfaces for touch/mouse input
5. Manages visual rendering order during drag operations

---

# Complete Script Code

```csharp
using System;
using System.Reflection;
using UnityEngine;
using UnityEngine.EventSystems;

public class Draggable : MonoBehaviour, IPointerDownHandler, IDragHandler, IPointerUpHandler
{
    public enum Mode { Placement, Placed }
    public Mode mode = Mode.Placement;

    // grid & callbacks
    float gridSize = 0.5f;
    Action<GameObject> onConfirm;
    Action<GameObject> onCancel;

    // reference to village manager (set in Init or found)
    [HideInInspector] public VillageManager villageManager;

    // runtime
    bool isDragging = false;
    int originalSortingOrder = int.MinValue;
    string originalSortingLayer = null;

    // ... methods follow
}
```

---

# Imports / Using Statements

## Line 1: `using System;`

```csharp
using System;
```

### What it does:

- Imports the core C# `System` namespace from the .NET framework
- This namespace contains fundamental types used in almost every C# program

### Why it's needed here:

- This script uses `Action<T>` delegate type (lines 13-14)
- `Action<GameObject>` represents a method that takes a GameObject parameter and returns nothing

### What `Action<T>` is:

```csharp
// Action<T> is a built-in delegate type
// It represents: void MethodName(T parameter)

Action<GameObject> onConfirm;  // Can hold any method like: void SomeMethod(GameObject go)
```

### What would happen if removed:

- **Compilation error:** `The type or namespace name 'Action' could not be found`

---

## Line 2: `using System.Reflection;`

```csharp
using System.Reflection;
```

### What it does:

- Imports the `Reflection` namespace
- Reflection allows code to inspect and invoke methods at runtime by name

### Why it's needed here:

- Used in `SafeFind<T>()` method (lines 25-56)
- Allows calling `FindFirstObjectByType` or `FindObjectOfType` dynamically
- Provides Unity version compatibility (different Unity versions have different methods)

### What Reflection provides:

- `MethodInfo` - Represents a method that can be invoked dynamically
- `BindingFlags` - Flags to control how methods are searched
- `GetMethod()` - Finds a method by name
- `Invoke()` - Calls a method dynamically

### Example of Reflection:

```csharp
// Instead of directly calling:
VillageManager vm = FindObjectOfType<VillageManager>();

// Reflection does this dynamically:
MethodInfo method = typeof(Object).GetMethod("FindObjectOfType");
MethodInfo generic = method.MakeGenericMethod(typeof(VillageManager));
object result = generic.Invoke(null, null);
VillageManager vm = result as VillageManager;
```

### What would happen if removed:

- **Compilation error:** `MethodInfo`, `BindingFlags` would be unrecognized
- The `SafeFind<T>()` helper method would not compile

---

## Line 3: `using UnityEngine;`

```csharp
using UnityEngine;
```

### What it does:

- Imports Unity's core engine namespace
- Contains ALL fundamental Unity types

### What it provides (used in this script):

| Type                          | Purpose in this script                |
| ----------------------------- | ------------------------------------- |
| `MonoBehaviour`               | Base class for Unity components       |
| `GameObject`                  | The objects being dragged             |
| `Transform`                   | Position/rotation/scale access        |
| `Vector3`                     | 3D position calculations              |
| `Camera`                      | Screen-to-world coordinate conversion |
| `Mathf`                       | Math functions (Round, Max)           |
| `Debug`                       | Console logging                       |
| `SpriteRenderer`              | Visual rendering component            |
| `Collider2D`, `BoxCollider2D` | Physics/input detection               |
| `HideInInspector`             | Attribute to hide public fields       |

### What would happen if removed:

- **Compilation error:** Almost every Unity-related type would be unrecognized
- The script would be completely non-functional

---

## Line 4: `using UnityEngine.EventSystems;`

```csharp
using UnityEngine.EventSystems;
```

### What it does:

- Imports Unity's Event System namespace
- Contains interfaces and classes for handling user input events

### What it provides (used in this script):

| Type                  | Purpose                                  |
| --------------------- | ---------------------------------------- |
| `IPointerDownHandler` | Interface for mouse/touch down events    |
| `IDragHandler`        | Interface for drag events                |
| `IPointerUpHandler`   | Interface for mouse/touch release events |
| `PointerEventData`    | Contains data about the pointer event    |

### What is the Event System:

- Unity's system for processing input events (mouse, touch, keyboard)
- Uses the **Observer Pattern** - objects register to receive events
- Requires an `EventSystem` GameObject in the scene

### What would happen if removed:

- **Compilation error:** The interfaces and `PointerEventData` would be unrecognized
- No drag-and-drop functionality would work

---

# Class Declaration

## Line 6: Class Definition with Interfaces

```csharp
public class Draggable : MonoBehaviour, IPointerDownHandler, IDragHandler, IPointerUpHandler
```

### Breaking this down piece by piece:

---

### `public`

**What it means:**

- This class is accessible from any other script
- Can be referenced by name: `Draggable`
- Can be added as a component via code: `AddComponent<Draggable>()`

---

### `class`

**What it means:**

- Declares a reference type (class) in C#
- Classes can contain fields, properties, methods, events

---

### `Draggable`

**What it means:**

- The unique identifier for this class
- Must match the filename exactly: `Draggable.cs`
- Descriptive name: objects of this class can be "dragged"

---

### `: MonoBehaviour`

**What it means:**

- Inherits from Unity's `MonoBehaviour` base class
- The colon `:` means "inherits from" or "extends"

**What MonoBehaviour provides:**

| Feature             | Description                                            |
| ------------------- | ------------------------------------------------------ |
| `gameObject`        | Reference to the GameObject this script is attached to |
| `transform`         | Reference to the Transform component                   |
| `GetComponent<T>()` | Find components on this GameObject                     |
| `AddComponent<T>()` | Add new components                                     |
| `Destroy()`         | Destroy GameObjects or components                      |
| Lifecycle methods   | `Awake()`, `Start()`, `Update()`, etc.                 |

**Why inherit from MonoBehaviour:**

- Required to attach this script to GameObjects
- Required for Unity's component system to work
- Required for lifecycle methods to be called

---

### `, IPointerDownHandler`

**What it means:**

- This class ALSO implements the `IPointerDownHandler` interface
- Multiple inheritance uses commas: `Base, Interface1, Interface2`

**What IPointerDownHandler requires:**

```csharp
public void OnPointerDown(PointerEventData eventData)
```

- Unity calls this method when the user clicks/touches this object
- The method MUST be public and have this exact signature

**When Unity calls OnPointerDown:**

1. User clicks/touches the screen
2. Unity performs a raycast from the click position
3. If the ray hits a Collider2D on this GameObject
4. AND this GameObject has a script implementing IPointerDownHandler
5. Unity calls `OnPointerDown()` on that script

---

### `, IDragHandler`

**What it means:**

- Also implements the `IDragHandler` interface

**What IDragHandler requires:**

```csharp
public void OnDrag(PointerEventData eventData)
```

- Called EVERY FRAME while the user is dragging (holding and moving)
- Only called if `OnPointerDown` was triggered first

**Example timing:**

```
Frame 1: OnPointerDown() called (user presses down)
Frame 2: OnDrag() called (user moves while held)
Frame 3: OnDrag() called (still moving)
Frame 4: OnDrag() called (still moving)
Frame 5: OnPointerUp() called (user releases)
```

---

### `, IPointerUpHandler`

**What it means:**

- Also implements the `IPointerUpHandler` interface

**What IPointerUpHandler requires:**

```csharp
public void OnPointerUp(PointerEventData eventData)
```

- Called when the user releases the mouse/touch
- Only called if the pointer was originally down on this object

---

### Requirements for these interfaces to work:

1. **Collider2D on the GameObject** - For raycasts to detect clicks
2. **EventSystem in the scene** - Processes all input events
3. **Physics2DRaycaster on the Camera** - Enables 2D raycasting for clicks

---

# Variables & Fields

## Lines 8-9: Mode Enum and Field

```csharp
public enum Mode { Placement, Placed }
public Mode mode = Mode.Placement;
```

### Line 8: `public enum Mode { Placement, Placed }`

**What is an enum:**

- Short for "enumeration"
- A type that defines a set of named constants
- Used when a variable can only be one of a few specific values

**Why use enum instead of strings:**

```csharp
// BAD: Using strings
string mode = "Placement";  // Typos possible: "placement", "Placment"
if (mode == "Placment") { }  // Bug! Typo not caught

// GOOD: Using enum
Mode mode = Mode.Placement;
if (mode == Mode.Placment) { }  // Compilation error! Typo caught
```

**The two modes:**
| Mode | Meaning | Behavior |
|------|---------|----------|
| `Placement` | Building is being placed | Can be dragged |
| `Placed` | Building is permanently placed | Click to delete |

### Line 9: `public Mode mode = Mode.Placement;`

**Breaking it down:**

- `public`: Visible in Inspector, accessible from other scripts
- `Mode`: The enum type we just defined
- `mode`: Variable name (lowercase, different from type)
- `= Mode.Placement`: Default value is Placement

**In the Inspector:**

- Shows as a dropdown with two options: "Placement" and "Placed"

---

## Lines 11-14: Grid and Callbacks

```csharp
// grid & callbacks
float gridSize = 0.5f;
Action<GameObject> onConfirm;
Action<GameObject> onCancel;
```

### Line 12: `float gridSize = 0.5f;`

**What it does:**

- Stores the grid snap increment
- Buildings will snap to multiples of 0.5 units

**Breaking it down:**

- `float`: Decimal number type (floating-point)
- `gridSize`: Variable name describing purpose
- `= 0.5f`: Default value (the `f` suffix indicates float literal)

**No access modifier (default private):**

- Without `public`, this is `private` by default
- NOT visible in Inspector
- Only accessible within this class

**How grid snapping works:**

```
If gridSize = 0.5:
- Drag to position 2.3 → snaps to 2.5
- Drag to position 2.1 → snaps to 2.0
- Drag to position 0.7 → snaps to 0.5
```

---

### Line 13: `Action<GameObject> onConfirm;`

**What it does:**

- Stores a reference to a callback method
- Called when building placement is confirmed

**What is Action<T>:**

- A delegate type (a "function pointer")
- Can hold a reference to any method matching the signature:
  - Takes one `GameObject` parameter
  - Returns `void` (nothing)

**Why use callbacks:**

- **Loose coupling**: This script doesn't need to know about VillageManager directly
- **Flexibility**: Different callers can provide different callbacks
- **Separation of concerns**: Draggable handles dragging; caller handles confirmation

**Example usage:**

```csharp
// In VillageManager:
draggable.Init(0.5f, OnPlacementConfirmed, OnPlacementCanceled, this);

// When placement is confirmed, Draggable calls:
onConfirm?.Invoke(gameObject);

// Which executes VillageManager.OnPlacementConfirmed(gameObject)
```

---

### Line 14: `Action<GameObject> onCancel;`

**What it does:**

- Stores a callback for when placement is canceled
- Called when user cancels building placement

---

## Lines 16-17: VillageManager Reference

```csharp
// reference to village manager (set in Init or found)
[HideInInspector] public VillageManager villageManager;
```

### Line 17: `[HideInInspector] public VillageManager villageManager;`

**Breaking it down:**

#### `[HideInInspector]`

**What it does:**

- An attribute that modifies how the field behaves
- Makes the field NOT appear in the Unity Inspector
- The field is still public (accessible from code)

**Why use this:**

- Field is set programmatically via `Init()` method
- Showing in Inspector would confuse designers
- Prevents accidental manual assignment

**Without HideInInspector:**

```
Inspector:
  Village Manager: None (VillageManager)  ← Confusing! Don't touch!
```

**With HideInInspector:**

```
Inspector:
  (field not shown)  ← Cleaner!
```

#### `public`

**Why public despite HideInInspector:**

- Other scripts need to set this value via `Init()`
- The Draggable script needs to call `villageManager.OnPlacedObjectClicked()`

#### `VillageManager`

**The type:**

- Reference to the VillageManager script
- Used to notify when placed objects are clicked

---

## Lines 19-22: Runtime State

```csharp
// runtime
bool isDragging = false;
int originalSortingOrder = int.MinValue;
string originalSortingLayer = null;
```

### Line 20: `bool isDragging = false;`

**What it does:**

- Tracks whether this object is currently being dragged
- `true` between OnPointerDown and OnPointerUp

**Why track this:**

- OnDrag only moves the object if isDragging is true
- Prevents unintended movement

---

### Line 21: `int originalSortingOrder = int.MinValue;`

**What it does:**

- Stores the original sorting order before dragging
- Used to restore after dragging ends

**What is sorting order:**

- Determines which sprites render in front of others
- Higher numbers render on top
- Used by SpriteRenderer component

**Why `int.MinValue` as default:**

- -2,147,483,648 (smallest possible int)
- Acts as a "not set" sentinel value
- Can check: `if (originalSortingOrder != int.MinValue)`

---

### Line 22: `string originalSortingLayer = null;`

**What it does:**

- Stores the original sorting layer name
- Sorting layers are named groups (e.g., "Background", "Default", "Foreground")

---

# Custom Functions

## Lines 25-56: SafeFind Helper Method

```csharp
static T SafeFind<T>() where T : UnityEngine.Object
{
    var objType = typeof(UnityEngine.Object);
    string[] methodNames = new string[] { "FindFirstObjectByType", "FindAnyObjectByType", "FindObjectOfType" };

    foreach (var name in methodNames)
    {
        MethodInfo mi = null;
        try
        {
            mi = objType.GetMethod(name, BindingFlags.Public | BindingFlags.Static);
        }
        catch { mi = null; }

        if (mi != null)
        {
            try
            {
                MethodInfo gen = mi.MakeGenericMethod(typeof(T));
                object res = gen.Invoke(null, null);
                return res as T;
            }
            catch { }
        }
    }

    return null;
}
```

### Line 25: Method Signature

```csharp
static T SafeFind<T>() where T : UnityEngine.Object
```

**Breaking it down:**

#### `static`

- Belongs to the class, not to instances
- Can be called without an object: `SafeFind<VillageManager>()`

#### `T`

- Generic type parameter
- Placeholder for any type specified when calling

#### `SafeFind<T>()`

- Generic method name
- `<T>` indicates it's generic

#### `where T : UnityEngine.Object`

- Type constraint
- T must inherit from `UnityEngine.Object`
- Ensures we can use Unity's Find methods

### Why this method exists:

**The Problem:**

```csharp
// Unity 2022 and earlier:
FindObjectOfType<T>()  // Works but shows obsolete warning in Unity 2023+

// Unity 2023+:
FindFirstObjectByType<T>()  // New preferred method

// Unity 6+:
FindAnyObjectByType<T>()  // Even newer method
```

**The Solution:**

- Use Reflection to try each method by name
- First one that works gets used
- Provides compatibility across Unity versions

---

## Lines 60-87: Init Method

```csharp
public void Init(float grid, Action<GameObject> confirmCallback, Action<GameObject> cancelCallback, VillageManager vm = null)
{
    gridSize = Mathf.Max(0.01f, grid);
    onConfirm = confirmCallback;
    onCancel = cancelCallback;

    if (vm != null)
    {
        villageManager = vm;
    }
    else
    {
        villageManager = SafeFind<VillageManager>();
    }

    if (GetComponent<Collider2D>() == null)
    {
        var sr = GetComponentInChildren<SpriteRenderer>();
        var bc = gameObject.AddComponent<BoxCollider2D>();
        bc.isTrigger = false;
        if (sr != null)
        {
            try { bc.size = sr.bounds.size; } catch { }
        }
    }
}
```

### Line 60: Method Signature

```csharp
public void Init(float grid, Action<GameObject> confirmCallback, Action<GameObject> cancelCallback, VillageManager vm = null)
```

**Parameters:**

| Parameter         | Type                 | Purpose                           |
| ----------------- | -------------------- | --------------------------------- |
| `grid`            | `float`              | Grid size for snapping            |
| `confirmCallback` | `Action<GameObject>` | Method to call when confirmed     |
| `cancelCallback`  | `Action<GameObject>` | Method to call when canceled      |
| `vm`              | `VillageManager`     | Optional VillageManager reference |

**`VillageManager vm = null`:**

- Optional parameter with default value
- If not provided, defaults to null
- Caller can omit this: `Init(0.5f, OnConfirm, OnCancel)`

### Line 62: `gridSize = Mathf.Max(0.01f, grid);`

**What it does:**

- Sets gridSize to the larger of 0.01 or the provided value
- Prevents gridSize from being 0 or negative (would cause division issues)

**Why Mathf.Max:**

```csharp
Mathf.Max(0.01f, 0.5f)   // Returns 0.5
Mathf.Max(0.01f, 0.0f)   // Returns 0.01 (prevents zero)
Mathf.Max(0.01f, -1.0f)  // Returns 0.01 (prevents negative)
```

### Lines 76-86: Auto-add Collider

```csharp
if (GetComponent<Collider2D>() == null)
{
    var sr = GetComponentInChildren<SpriteRenderer>();
    var bc = gameObject.AddComponent<BoxCollider2D>();
    bc.isTrigger = false;
    if (sr != null)
    {
        try { bc.size = sr.bounds.size; } catch { }
    }
}
```

**What this does:**

1. Check if Collider2D exists
2. If not, find the SpriteRenderer (for sizing)
3. Add a BoxCollider2D component
4. Size it to match the sprite

**Why this is needed:**

- Pointer events require a Collider2D to detect clicks
- Saves designers from manually adding colliders to every prefab

---

## Lines 90-112: OnPointerDown

```csharp
public void OnPointerDown(PointerEventData eventData)
{
    Debug.Log($"Draggable: OnPointerDown => name={gameObject.name} mode={mode} vm={(villageManager != null)}");

    if (mode == Mode.Placed)
    {
        if (villageManager != null)
        {
            Debug.Log("Draggable: calling villageManager.OnPlacedObjectClicked");
            villageManager.OnPlacedObjectClicked(gameObject);
        }
        else
        {
            Debug.LogWarning("Draggable: villageManager is null on this instance.");
        }
        return;
    }

    isDragging = true;
    BringToFront();
}
```

### When Unity calls this:

1. User clicks/touches the screen
2. EventSystem performs Physics2D raycast
3. Raycast hits this object's Collider2D
4. Unity calls this method

### Two behaviors based on mode:

**If mode == Placed:**

- Building is already placed
- Tell VillageManager to select for deletion
- `return` early - don't start dragging

**If mode == Placement:**

- Building is being placed
- Set `isDragging = true`
- Call `BringToFront()` to render on top

---

## Lines 114-123: OnDrag

```csharp
public void OnDrag(PointerEventData eventData)
{
    if (!isDragging || mode != Mode.Placement) return;

    Vector3 world = Camera.main.ScreenToWorldPoint(eventData.position);
    world.z = 0f;
    float snappedX = Mathf.Round(world.x / gridSize) * gridSize;
    float snappedY = Mathf.Round(world.y / gridSize) * gridSize;
    transform.position = new Vector3(snappedX, snappedY, transform.position.z);
}
```

### Line 116: Guard clause

```csharp
if (!isDragging || mode != Mode.Placement) return;
```

- Exit early if not dragging or already placed
- Prevents unintended movement

### Line 118: Screen to World conversion

```csharp
Vector3 world = Camera.main.ScreenToWorldPoint(eventData.position);
```

**What this does:**

- `eventData.position`: Mouse/touch position in SCREEN coordinates (pixels)
- `Camera.main`: The main camera in the scene
- `ScreenToWorldPoint()`: Converts screen position to world position

**Screen vs World coordinates:**

```
Screen: (540, 960) pixels from bottom-left
World: (0, 5, -10) units in game space
```

### Line 119: Fix Z coordinate

```csharp
world.z = 0f;
```

- ScreenToWorldPoint returns a Z based on camera distance
- We want Z = 0 for 2D games

### Lines 120-121: Grid snapping

```csharp
float snappedX = Mathf.Round(world.x / gridSize) * gridSize;
float snappedY = Mathf.Round(world.y / gridSize) * gridSize;
```

**How snapping works:**

| Step             | Example (gridSize = 0.5, world.x = 2.3) |
| ---------------- | --------------------------------------- |
| Divide by grid   | 2.3 / 0.5 = 4.6                         |
| Round            | Round(4.6) = 5                          |
| Multiply by grid | 5 \* 0.5 = 2.5                          |
| **Result**       | 2.3 snaps to 2.5                        |

### Line 122: Apply position

```csharp
transform.position = new Vector3(snappedX, snappedY, transform.position.z);
```

- Create new Vector3 with snapped X and Y
- Keep original Z (for layering)

---

## Lines 125-135: OnPointerUp

```csharp
public void OnPointerUp(PointerEventData eventData)
{
    if (mode != Mode.Placement) return;

    isDragging = false;

    onConfirm?.Invoke(gameObject);

    RestoreSortingOrder();
}
```

### Line 132: Invoke callback

```csharp
onConfirm?.Invoke(gameObject);
```

**What `?.` does:**

- Null-conditional operator
- Only calls Invoke if onConfirm is not null

**What Invoke does:**

- Calls the stored callback method
- Passes `gameObject` as the parameter
- This triggers VillageManager.OnPlacementConfirmed

---

## Lines 138-157: BringToFront and RestoreSortingOrder

```csharp
void BringToFront()
{
    var sr = GetComponent<SpriteRenderer>();
    if (sr != null)
    {
        originalSortingOrder = sr.sortingOrder;
        originalSortingLayer = sr.sortingLayerName;
        sr.sortingOrder = 1000;
    }
}

void RestoreSortingOrder()
{
    var sr = GetComponent<SpriteRenderer>();
    if (sr != null && originalSortingOrder != int.MinValue)
    {
        sr.sortingOrder = originalSortingOrder;
        if (!string.IsNullOrEmpty(originalSortingLayer))
            sr.sortingLayerName = originalSortingLayer;
    }
}
```

**Purpose:**

- While dragging, render the building on TOP of everything else
- After dragging, restore original rendering order

**Why sorting order 1000:**

- Typical objects have sorting order 0-10
- 1000 guarantees it renders on top of everything

---

## Lines 160-170: SetPlaced and CancelPlacement

```csharp
public void SetPlaced()
{
    mode = Mode.Placed;
    isDragging = false;
}

public void CancelPlacement()
{
    onCancel?.Invoke(gameObject);
}
```

### SetPlaced:

- Called by VillageManager after placement confirmed
- Switches mode so clicking now triggers deletion flow

### CancelPlacement:

- Called externally to cancel placement
- Invokes the cancel callback

---

# How Everything Works Together

## Complete Placement Flow

```
1. Player clicks building in inventory
   ↓
2. VillageManager.SpawnBuildingFromData() creates building prefab
   ↓
3. VillageManager calls draggable.Init(gridSize, OnConfirm, OnCancel, this)
   ↓
4. Player touches the building
   ↓
5. OnPointerDown() called
   - mode == Placement, so isDragging = true
   - BringToFront() → sortingOrder = 1000
   ↓
6. Player drags finger/mouse
   ↓
7. OnDrag() called every frame
   - Convert screen position to world position
   - Snap to grid
   - Move building to snapped position
   ↓
8. Player releases
   ↓
9. OnPointerUp() called
   - isDragging = false
   - onConfirm?.Invoke(gameObject) → calls VillageManager.OnPlacementConfirmed
   - RestoreSortingOrder()
   ↓
10. VillageManager.OnPlacementConfirmed()
    - Spends GreenPoints
    - Calls draggable.SetPlaced() → mode = Placed
    - Saves village
```

## Click-to-Delete Flow (After Placed)

```
1. Player clicks placed building
   ↓
2. OnPointerDown() called
   - mode == Placed
   - villageManager.OnPlacedObjectClicked(gameObject)
   - return (no dragging)
   ↓
3. VillageManager forwards to DeleteManager
   ↓
4. DeleteManager shows confirmation popup
```

---

# Common Mistakes & Notes

## Mistake 1: No Collider2D

**Problem:** Clicks don't register
**Fix:** Init() auto-adds BoxCollider2D, but you can add one to prefab

## Mistake 2: No EventSystem

**Problem:** No pointer events work at all
**Fix:** Add GameObject > UI > EventSystem to scene

## Mistake 3: No Physics2DRaycaster

**Problem:** Clicks on 2D objects don't work
**Fix:** Add Physics2DRaycaster component to Camera

## Mistake 4: Camera.main is null

**Problem:** NullReferenceException in OnDrag
**Fix:** Ensure camera has tag "MainCamera"

---

# Summary

| Method              | When Called       | Purpose                           |
| ------------------- | ----------------- | --------------------------------- |
| `Init()`            | After Instantiate | Setup grid, callbacks, collider   |
| `OnPointerDown()`   | User clicks       | Start drag or trigger delete flow |
| `OnDrag()`          | User drags        | Move with grid snapping           |
| `OnPointerUp()`     | User releases     | Confirm placement                 |
| `SetPlaced()`       | After confirm     | Lock building in place            |
| `CancelPlacement()` | External cancel   | Trigger cancel callback           |
