# 📚 VillageManager.cs - Complete Line-by-Line Documentation

**File Path:** `Assets/Scripts/Village/VillageManager.cs`

This document provides an **extremely detailed, line-by-line explanation** of the VillageManager script, teaching all Unity concepts from the ground up.

---

# Script Purpose

The `VillageManager` script is the **central controller** for the Village scene. It manages:

1. **Building Inventory** - Generates UI buttons for all available buildings
2. **Building Spawning** - Creates building instances when player selects from inventory
3. **Placement Flow** - Handles confirm/cancel UI for building placement
4. **GreenPoints Economy** - Checks affordability and spends currency
5. **Message System** - Displays feedback messages to the player
6. **Coordination** - Connects with SaveManager, DeleteManager, and AudioManager

This is a **Manager/Controller Pattern** script - it coordinates multiple systems without directly implementing low-level behavior.

---

# Complete Script Code

```csharp
using System.Collections;
using System.Reflection;
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class VillageManager : MonoBehaviour
{
    [Header("References")]
    public Transform placeRoot;
    public Transform inventoryContent;
    public GameObject inventoryItemPrefab;

    [Header("Data")]
    public System.Collections.Generic.List<BuildingData> allBuildingData = new System.Collections.Generic.List<BuildingData>();
    public System.Collections.Generic.List<GameObject> buildingPrefabs = new System.Collections.Generic.List<GameObject>();

    [Header("Placement")]
    public float gridSize = 0.5f;
    public Vector2 defaultSpawnScale = new Vector2(1f, 1f);

    [Header("Confirm UI")]
    public GameObject panelConfirmPlacement;
    public Button confirmButton;
    public Button cancelButton;

    [Header("Message UI")]
    public GameObject messagePanel;
    public TextMeshProUGUI messageText;
    public float messageDuration = 2f;

    [Header("Optional Systems")]
    public SaveManager saveManager;
    public DeleteManager deleteManager;
    public AudioManager audioManager;

    private GameObject currentObject = null;
    private Draggable currentDraggable = null;
    private Coroutine messageCoroutine;

    // ... methods follow
}
```

---

# Imports / Using Statements

## Line 1: `using System.Collections;`

```csharp
using System.Collections;
```

### What it does:

- Imports the base Collections namespace from the .NET framework
- This namespace contains fundamental collection interfaces and base classes

### Why it's needed here:

- Required for using `IEnumerator`, which is the return type for coroutines
- The `HideMessageAfterDelay()` coroutine (line 285) returns `IEnumerator`

### What is IEnumerator:

```csharp
// IEnumerator is an interface that allows iterating through a collection
// Unity "hijacks" this for coroutines - pausing and resuming execution

private IEnumerator HideMessageAfterDelay()
{
    yield return new WaitForSeconds(2f);  // Pause here for 2 seconds
    // Then continue execution
}
```

### What would happen if removed:

- **Compilation error:** `The type or namespace name 'IEnumerator' could not be found`
- All coroutines in this script would fail to compile

---

## Line 2: `using System.Reflection;`

```csharp
using System.Reflection;
```

### What it does:

- Imports the Reflection namespace for runtime type inspection
- Allows code to examine and invoke methods dynamically

### Why it's here:

- This import is **not actually used** in the current version of VillageManager
- It may have been used previously or was imported for future use
- It doesn't cause any harm but is unnecessary

### What would happen if removed:

- **No effect** - the script would compile and run identically
- Good practice: Remove unused imports to keep code clean

---

## Line 3: `using UnityEngine;`

```csharp
using UnityEngine;
```

### What it does:

- Imports Unity's core engine namespace
- Contains ALL fundamental Unity types and functionality

### What it provides (used in this script):

| Type                 | Purpose in this script                 |
| -------------------- | -------------------------------------- |
| `MonoBehaviour`      | Base class for Unity components        |
| `GameObject`         | Building instances, prefabs, UI panels |
| `Transform`          | Position, parent-child relationships   |
| `Vector2`, `Vector3` | Positions and scales                   |
| `Camera`             | Screen-to-world coordinate conversion  |
| `Screen`             | Screen dimensions                      |
| `Input`              | Mouse position                         |
| `Mathf`              | Mathematical functions                 |
| `Color`              | Message text colors                    |
| `Debug`              | Console logging                        |

### What would happen if removed:

- **Compilation error:** Every Unity type would be unrecognized
- The script would be completely non-functional

---

## Line 4: `using UnityEngine.UI;`

```csharp
using UnityEngine.UI;
```

### What it does:

- Imports Unity's UI (User Interface) namespace
- Contains all UI component types

### What it provides (used in this script):

| Type     | Usage                                                   |
| -------- | ------------------------------------------------------- |
| `Button` | `confirmButton`, `cancelButton` - clickable UI elements |

### How Button works:

```csharp
public Button confirmButton;  // Reference to a Button component

// In Start():
confirmButton.onClick.AddListener(ConfirmCurrentPlacement);
// When clicked, Unity calls ConfirmCurrentPlacement()
```

### What would happen if removed:

- **Compilation error:** `Button` type would be unrecognized
- Cannot reference or configure UI buttons

---

## Line 5: `using TMPro;`

```csharp
using TMPro;
```

### What it does:

- Imports TextMeshPro namespace
- TextMeshPro is Unity's advanced text rendering system

### What it provides:

| Type              | Usage                                          |
| ----------------- | ---------------------------------------------- |
| `TextMeshProUGUI` | `messageText` - high-quality UI text component |

### Why TextMeshPro instead of regular Text:

- **Better quality:** Sharp text at any size
- **More features:** Rich text, gradients, outlines
- **Better performance:** Optimized rendering
- **Industry standard:** Used in professional games

### What would happen if removed:

- **Compilation error:** `TextMeshProUGUI` would be unrecognized
- Cannot display text messages

---

# Class Declaration

## Line 11: Class Definition

```csharp
public class VillageManager : MonoBehaviour
```

### Breaking this down:

---

### `public`

**What it means:**

- This class is accessible from any other script
- Other scripts can get a reference: `VillageManager vm = GetComponent<VillageManager>();`

**Why public:**

- Other scripts need to interact with VillageManager:
  - `InventoryButton` calls `SpawnBuildingFromData()`
  - `Draggable` calls `OnPlacedObjectClicked()`
  - `SaveManager` might need references

---

### `class`

**What it means:**

- Declares a reference type in C#
- Classes can contain fields, properties, methods, events

---

### `VillageManager`

**What it means:**

- The unique identifier for this class
- **Must match the filename exactly:** `VillageManager.cs`
- Naming convention: PascalCase, descriptive name
- "Manager" suffix indicates this is a controller/coordinator class

---

### `: MonoBehaviour`

**What it means:**

- Inherits from Unity's `MonoBehaviour` base class
- The colon `:` means "inherits from" or "extends"

**What MonoBehaviour provides:**

| Feature             | Description                                  |
| ------------------- | -------------------------------------------- |
| `gameObject`        | The GameObject this script is attached to    |
| `transform`         | The Transform component of this GameObject   |
| `GetComponent<T>()` | Find components on this or other GameObjects |
| `Instantiate()`     | Create copies of prefabs/objects             |
| `Destroy()`         | Destroy GameObjects or components            |
| `StartCoroutine()`  | Start asynchronous operations                |
| `StopCoroutine()`   | Stop running coroutines                      |
| Lifecycle methods   | `Awake()`, `Start()`, `Update()`, etc.       |

**Why inherit from MonoBehaviour:**

- Required to attach this script to a GameObject in the scene
- Required for Unity's component lifecycle to work
- Required for coroutines to function

---

# Variables & Fields

## Lines 13-16: References Header

```csharp
[Header("References")]
public Transform placeRoot;
public Transform inventoryContent;
public GameObject inventoryItemPrefab;
```

### Line 13: `[Header("References")]`

**What it does:**

- Creates a visual header/divider in the Unity Inspector
- Groups related fields under a labeled section
- Makes the Inspector more organized and readable

**In the Inspector:**

```
▼ References
   Place Root: (Transform)
   Inventory Content: (Transform)
   Inventory Item Prefab: (GameObject)
```

---

### Line 14: `public Transform placeRoot;`

**What it does:**

- Stores a reference to a Transform in the scene
- All spawned buildings become children of this Transform

**Why use a "root" Transform:**

- **Organization:** Keeps all buildings under one parent
- **Easy management:** Can find all buildings with `placeRoot.GetChild(i)`
- **Scene cleanliness:** Buildings don't clutter the scene hierarchy
- **Performance:** Can disable/enable all buildings at once

**How it's used:**

```csharp
GameObject obj = Instantiate(prefab, placeRoot);  // Line 156
// Building becomes child of placeRoot
```

**What must exist in scene:**

- An empty GameObject (e.g., "Buildings" or "PlaceRoot")
- Drag this GameObject into the `placeRoot` field

---

### Line 15: `public Transform inventoryContent;`

**What it does:**

- Reference to the Transform that holds inventory buttons
- Usually the Content object inside a Scroll View

**Typical UI hierarchy:**

```
Canvas
└── InventoryPanel
    └── Scroll View
        └── Viewport
            └── Content  ← This is inventoryContent
                ├── InventoryItem_House (spawned)
                ├── InventoryItem_Tree (spawned)
                └── InventoryItem_Windmill (spawned)
```

**Why Transform instead of RectTransform:**

- Transform is the base class
- Works for both 2D and 3D objects
- RectTransform inherits from Transform

---

### Line 16: `public GameObject inventoryItemPrefab;`

**What it does:**

- The prefab template for inventory buttons
- Instantiated once for each BuildingData

**What the prefab should contain:**

- A UI Button component
- An `InventoryButton` script
- An Image for the building icon
- A Text/TMP for the building name

---

## Lines 18-20: Data Header

```csharp
[Header("Data")]
public System.Collections.Generic.List<BuildingData> allBuildingData = new System.Collections.Generic.List<BuildingData>();
public System.Collections.Generic.List<GameObject> buildingPrefabs = new System.Collections.Generic.List<GameObject>();
```

### Line 19: `public System.Collections.Generic.List<BuildingData> allBuildingData`

**What it does:**

- A list of all BuildingData ScriptableObjects
- Each entry defines a building type (House, Tree, Windmill, etc.)

**Why `System.Collections.Generic.List<T>`:**

- Fully qualified name for `List<T>`
- Could also write: `using System.Collections.Generic;` at top
- Then just use: `List<BuildingData>`

**Why `= new System.Collections.Generic.List<BuildingData>()`:**

- Initializes the list to empty (not null)
- Prevents NullReferenceException if accessed before assignment
- Unity can serialize and show in Inspector

**What is BuildingData:**

- A ScriptableObject defining building properties
- Contains: displayName, icon, greenPointsCost, refund amount

**In the Inspector:**

```
▼ All Building Data
   Size: 5
   Element 0: House (BuildingData)
   Element 1: Tree (BuildingData)
   Element 2: Windmill (BuildingData)
   Element 3: SolarPanel (BuildingData)
   Element 4: WaterTank (BuildingData)
```

---

### Line 20: `public System.Collections.Generic.List<GameObject> buildingPrefabs`

**What it does:**

- List of building prefabs (the actual 3D/2D models)
- Must be in same order as `allBuildingData`

**Why separate lists:**

- BuildingData contains metadata (cost, name, icon)
- Prefabs contain the actual visual/physical building
- Keeps data separate from visual representation

**How they connect:**

```csharp
// Line 132-134:
int idx = allBuildingData.IndexOf(data);  // Find index of BuildingData
if (idx >= 0 && idx < buildingPrefabs.Count)
    prefab = buildingPrefabs[idx];  // Get matching prefab at same index
```

---

## Lines 22-24: Placement Header

```csharp
[Header("Placement")]
public float gridSize = 0.5f;
public Vector2 defaultSpawnScale = new Vector2(1f, 1f);
```

### Line 23: `public float gridSize = 0.5f;`

**What it does:**

- Defines the grid snap increment for building placement
- Buildings will snap to multiples of 0.5 units

**How it's used:**

```csharp
dr.Init(gridSize, OnPlacementConfirmed, OnPlacementCanceled, this);
// Draggable uses this to snap positions
```

**Example grid snapping:**
| Drag Position | Grid Size | Snapped Position |
|---------------|-----------|------------------|
| 2.3 | 0.5 | 2.5 |
| 2.1 | 0.5 | 2.0 |
| 3.7 | 0.5 | 3.5 |

---

### Line 24: `public Vector2 defaultSpawnScale = new Vector2(1f, 1f);`

**What it does:**

- The default scale for newly spawned buildings
- (1, 1) means normal size

**What is Vector2:**

- A 2D vector with x and y components
- Used for 2D positions, sizes, directions

**How it's used:**

```csharp
obj.transform.localScale = defaultSpawnScale;  // Line 158
```

---

## Lines 26-29: Confirm UI Header

```csharp
[Header("Confirm UI")]
public GameObject panelConfirmPlacement;
public Button confirmButton;
public Button cancelButton;
```

### Line 27: `public GameObject panelConfirmPlacement;`

**What it does:**

- Reference to the confirmation panel UI
- Shown when a building is being placed
- Contains confirm and cancel buttons

**Why GameObject not a specific component:**

- We only need to show/hide it: `SetActive(true/false)`
- Don't need any specific component functionality

---

### Line 28-29: Buttons

```csharp
public Button confirmButton;
public Button cancelButton;
```

**What they do:**

- References to the Button components
- Used to attach click listeners

**How buttons work in Unity:**

1. **Button component** has an `onClick` event
2. You attach **listeners** (methods) to this event
3. When clicked, Unity calls all attached listeners

```csharp
// In Start():
confirmButton.onClick.AddListener(ConfirmCurrentPlacement);
// Now when confirmButton is clicked:
// Unity automatically calls ConfirmCurrentPlacement()
```

---

## Lines 31-37: Message UI Header

```csharp
[Header("Message UI")]
[Tooltip("Panel to show messages (success/fail)")]
public GameObject messagePanel;
[Tooltip("Text to display message")]
public TextMeshProUGUI messageText;
[Tooltip("How long message stays visible")]
public float messageDuration = 2f;
```

### Line 32: `[Tooltip("Panel to show messages (success/fail)")]`

**What it does:**

- Displays help text when hovering over the field in Inspector
- Helps designers understand what to assign

**In Inspector:**

```
Message Panel: (GameObject)  [Hover: "Panel to show messages (success/fail)"]
```

---

### Line 33: `public GameObject messagePanel;`

**What it does:**

- The panel that contains the message text
- Shown/hidden to display feedback to player

---

### Line 35: `public TextMeshProUGUI messageText;`

**What it does:**

- The actual text component that displays messages
- Set via `messageText.text = "Your message"`
- Color changed via `messageText.color = Color.green`

**What is TextMeshProUGUI:**

- TextMeshPro version of text for UI (not 3D world text)
- "UGUI" = Unity GUI (the canvas-based UI system)

---

### Line 37: `public float messageDuration = 2f;`

**What it does:**

- How long (in seconds) the message stays visible
- After this time, the message panel hides

---

## Lines 39-42: Optional Systems Header

```csharp
[Header("Optional Systems")]
public SaveManager saveManager;
public DeleteManager deleteManager;
public AudioManager audioManager;
```

### What these do:

- References to other manager scripts
- "Optional" because the game won't crash if they're null
- The script checks for null before using them

**Why optional:**

```csharp
saveManager?.SaveVillage();  // Only calls if saveManager is not null
```

The `?.` (null-conditional operator) prevents NullReferenceException.

---

## Lines 44-46: Private State

```csharp
private GameObject currentObject = null;
private Draggable currentDraggable = null;
private Coroutine messageCoroutine;
```

### Line 44: `private GameObject currentObject = null;`

**What it does:**

- Tracks the building currently being placed
- `null` when no building is being placed
- Set when `SpawnBuildingFromData()` creates a building
- Cleared when placement is confirmed or canceled

**Why track this:**

- Need to know what building to confirm/cancel
- Prevents multiple buildings being placed simultaneously

---

### Line 45: `private Draggable currentDraggable = null;`

**What it does:**

- Reference to the Draggable component of currentObject
- Cached for efficiency (avoid repeated GetComponent calls)

---

### Line 46: `private Coroutine messageCoroutine;`

**What it does:**

- Stores reference to the running message coroutine
- Used to stop the previous coroutine before starting a new one

**Why store coroutine reference:**

```csharp
if (messageCoroutine != null)
    StopCoroutine(messageCoroutine);  // Stop previous message timer

messageCoroutine = StartCoroutine(HideMessageAfterDelay());  // Start new one
```

Without this, overlapping messages would cause unpredictable behavior.

---

# Unity Lifecycle Methods

## Lines 48-69: Start Method

```csharp
void Start()
{
    if (panelConfirmPlacement != null)
        panelConfirmPlacement.SetActive(false);

    if (messagePanel != null)
        messagePanel.SetActive(false);

    if (confirmButton != null)
    {
        confirmButton.onClick.RemoveAllListeners();
        confirmButton.onClick.AddListener(ConfirmCurrentPlacement);
    }

    if (cancelButton != null)
    {
        cancelButton.onClick.RemoveAllListeners();
        cancelButton.onClick.AddListener(CancelCurrentPlacement);
    }

    PopulateInventory();
}
```

### When Unity calls Start():

- Called **once** when the script is first enabled
- Called **after** all `Awake()` methods have completed
- Called **before** the first `Update()`

### Line 50-51: Hide confirmation panel

```csharp
if (panelConfirmPlacement != null)
    panelConfirmPlacement.SetActive(false);
```

**What it does:**

- Hides the placement confirmation UI at game start
- The panel should only appear when placing a building

**Why null check:**

- Prevents NullReferenceException if not assigned in Inspector
- Makes the script more robust

**What is SetActive():**

- Enables/disables a GameObject
- `SetActive(false)` = invisible, inactive, Update() not called
- `SetActive(true)` = visible, active, Update() called

---

### Line 53-54: Hide message panel

```csharp
if (messagePanel != null)
    messagePanel.SetActive(false);
```

**What it does:**

- Ensures message panel is hidden at start
- Messages only appear when triggered

---

### Lines 56-60: Setup confirm button

```csharp
if (confirmButton != null)
{
    confirmButton.onClick.RemoveAllListeners();
    confirmButton.onClick.AddListener(ConfirmCurrentPlacement);
}
```

**Line 58: `confirmButton.onClick.RemoveAllListeners();`**

**What it does:**

- Removes ALL previously attached click handlers
- Clears any listeners set in the Editor or previous runs

**Why remove before adding:**

- Prevents duplicate listeners
- If Start() ran twice, without RemoveAll, you'd have 2 listeners
- Each click would call the method twice

**Line 59: `confirmButton.onClick.AddListener(ConfirmCurrentPlacement);`**

**What it does:**

- Attaches `ConfirmCurrentPlacement()` method to the button's click event
- When button is clicked, Unity calls `ConfirmCurrentPlacement()`

**How button events work:**

```
User clicks button → Unity detects click → Unity calls all listeners → ConfirmCurrentPlacement() runs
```

---

### Lines 62-66: Setup cancel button

```csharp
if (cancelButton != null)
{
    cancelButton.onClick.RemoveAllListeners();
    cancelButton.onClick.AddListener(CancelCurrentPlacement);
}
```

**Same pattern as confirm button**, but calls `CancelCurrentPlacement()`.

---

### Line 68: `PopulateInventory();`

**What it does:**

- Calls the method to create all inventory buttons
- Fills the inventory UI with building options

---

# Custom Functions

## Lines 71-104: PopulateInventory Method

```csharp
public void PopulateInventory()
{
    if (inventoryContent == null || inventoryItemPrefab == null)
    {
        Debug.LogWarning("VillageManager: inventoryContent or inventoryItemPrefab not assigned.");
        return;
    }

    for (int i = inventoryContent.childCount - 1; i >= 0; i--)
        Destroy(inventoryContent.GetChild(i).gameObject);

    for (int i = 0; i < allBuildingData.Count; i++)
    {
        var data = allBuildingData[i];
        var go = Instantiate(inventoryItemPrefab, inventoryContent);
        go.name = $"InventoryItem_{data.displayName}";

        var ib = go.GetComponent<InventoryButton>();
        if (ib == null)
        {
            Debug.LogError("VillageManager: InventoryItem prefab missing InventoryButton.");
            continue;
        }

        ib.Init(data, this);

        var btn = go.GetComponent<Button>();
        if (btn != null)
        {
            btn.onClick.RemoveAllListeners();
            btn.onClick.AddListener(ib.OnClickPlace);
        }
    }
}
```

### Line 71: Method signature

```csharp
public void PopulateInventory()
```

**Why public:**

- Could be called from external scripts (e.g., after loading save data)
- Could be called from UI button to "refresh" inventory

---

### Lines 73-77: Null check and early return

```csharp
if (inventoryContent == null || inventoryItemPrefab == null)
{
    Debug.LogWarning("VillageManager: inventoryContent or inventoryItemPrefab not assigned.");
    return;
}
```

**What it does:**

- Validates that required references are assigned
- Logs a warning if something is missing
- Returns early to prevent null reference errors

**Why Debug.LogWarning:**

- Yellow message in Console
- Indicates a problem but not a crash
- Helps designers know what to assign

---

### Lines 79-80: Clear existing children

```csharp
for (int i = inventoryContent.childCount - 1; i >= 0; i--)
    Destroy(inventoryContent.GetChild(i).gameObject);
```

**What it does:**

- Destroys all existing inventory items
- Clears the inventory before regenerating

**Why loop backwards (i-- instead of i++):**

```csharp
// PROBLEM with forward loop:
// Child 0: ItemA    Delete ItemA
// Child 0: ItemB    (was Child 1, now Child 0)
// Child 1: ItemC    Try to delete Child 1, which is now ItemC
// Some items get skipped!

// SOLUTION with backward loop:
// Child 2: ItemC    Delete ItemC
// Child 1: ItemB    Delete ItemB (still at index 1)
// Child 0: ItemA    Delete ItemA (still at index 0)
// All items deleted correctly!
```

**What is childCount:**

- Property of Transform
- Returns the number of child objects

**What is GetChild(i):**

- Gets the child Transform at index i
- Returns Transform, so we need `.gameObject` to get the GameObject

---

### Lines 82-103: Create inventory buttons

```csharp
for (int i = 0; i < allBuildingData.Count; i++)
{
    var data = allBuildingData[i];
    var go = Instantiate(inventoryItemPrefab, inventoryContent);
    go.name = $"InventoryItem_{data.displayName}";

    var ib = go.GetComponent<InventoryButton>();
    if (ib == null)
    {
        Debug.LogError("VillageManager: InventoryItem prefab missing InventoryButton.");
        continue;
    }

    ib.Init(data, this);

    var btn = go.GetComponent<Button>();
    if (btn != null)
    {
        btn.onClick.RemoveAllListeners();
        btn.onClick.AddListener(ib.OnClickPlace);
    }
}
```

**Line 84: `var data = allBuildingData[i];`**

- Get BuildingData at current index
- `var` = compiler infers type (BuildingData)

**Line 85: `var go = Instantiate(inventoryItemPrefab, inventoryContent);`**

**What Instantiate does:**

- Creates a copy of the prefab
- Second parameter sets the parent Transform
- Returns the new GameObject

**Why pass parent:**

- New object becomes child of inventoryContent
- Appears in the inventory UI
- Inherits RectTransform positioning

**Line 86: `go.name = $"InventoryItem_{data.displayName}";`**

- Sets the name in hierarchy
- Uses string interpolation: `$"text {variable}"`
- Result: "InventoryItem_House", "InventoryItem_Tree", etc.

**Lines 88-93: Get and validate InventoryButton**

```csharp
var ib = go.GetComponent<InventoryButton>();
if (ib == null)
{
    Debug.LogError("VillageManager: InventoryItem prefab missing InventoryButton.");
    continue;
}
```

- `continue` skips to next loop iteration
- Prevents crash if prefab is misconfigured

**Line 95: `ib.Init(data, this);`**

- Initialize the InventoryButton with:
  - `data`: The BuildingData for this button
  - `this`: Reference to VillageManager

**Lines 97-101: Setup button click**

```csharp
var btn = go.GetComponent<Button>();
if (btn != null)
{
    btn.onClick.RemoveAllListeners();
    btn.onClick.AddListener(ib.OnClickPlace);
}
```

When clicked, calls `ib.OnClickPlace()`, which eventually calls `SpawnBuildingFromData()`.

---

## Lines 109-174: SpawnBuildingFromData Method

```csharp
public void SpawnBuildingFromData(BuildingData data, bool spawnAtCenter = false)
{
    // ... detailed implementation
}
```

### Line 109: Method signature

```csharp
public void SpawnBuildingFromData(BuildingData data, bool spawnAtCenter = false)
```

**Parameters:**

| Parameter       | Type           | Default    | Purpose                                                      |
| --------------- | -------------- | ---------- | ------------------------------------------------------------ |
| `data`          | `BuildingData` | (required) | The building configuration to spawn                          |
| `spawnAtCenter` | `bool`         | `false`    | If true, spawn at screen center; otherwise at mouse position |

**`bool spawnAtCenter = false`:**

- Optional parameter with default value
- Can call: `SpawnBuildingFromData(data)` - uses default false
- Or call: `SpawnBuildingFromData(data, true)` - spawns at center

---

### Lines 111-115: Null check

```csharp
if (data == null)
{
    Debug.LogError("VillageManager: building data is null!");
    return;
}
```

**Defensive programming:**

- Validates input before processing
- `Debug.LogError` = red message in Console
- Prevents NullReferenceException

---

### Lines 117-128: Check affordability

```csharp
// Check if player can afford (100 GP)
if (GreenPointsManager.Instance != null)
{
    if (!GreenPointsManager.Instance.CanAfford(data.greenPointsCost))
    {
        // Show failure message
        ShowMessage($"Not enough GreenPoints!\nNeed {data.greenPointsCost} GP", Color.red);
        audioManager?.PlayCancel();
        Debug.LogWarning($"VillageManager: Cannot afford {data.displayName}! Need {data.greenPointsCost} GP");
        return;
    }
}
```

**What this does:**

1. Check if GreenPointsManager exists (singleton pattern)
2. Check if player can afford the building cost
3. If not affordable:
   - Show red error message
   - Play cancel sound
   - Log warning
   - Exit function early (don't spawn)

**What is `GreenPointsManager.Instance`:**

- Singleton pattern: One global instance accessible via `.Instance`
- `CanAfford(amount)` returns `true` if player has enough points

**What is `audioManager?.PlayCancel()`:**

- Null-conditional operator
- Only calls `PlayCancel()` if audioManager is not null
- Equivalent to: `if (audioManager != null) audioManager.PlayCancel();`

**`\n` in string:**

- Newline character
- Message appears on two lines in UI

---

### Lines 130-143: Find the prefab

```csharp
// Find prefab
GameObject prefab = null;
int idx = allBuildingData.IndexOf(data);
if (idx >= 0 && idx < buildingPrefabs.Count)
    prefab = buildingPrefabs[idx];

if (prefab == null)
    prefab = buildingPrefabs.Find(p => p != null && p.name == data.displayName);

if (prefab == null)
{
    Debug.LogError($"VillageManager: No prefab found for '{data.displayName}'");
    return;
}
```

**Line 132: `int idx = allBuildingData.IndexOf(data);`**

- Finds the index of `data` in `allBuildingData` list
- Returns -1 if not found

**Lines 133-134: Try index matching**

```csharp
if (idx >= 0 && idx < buildingPrefabs.Count)
    prefab = buildingPrefabs[idx];
```

- If valid index, get prefab at same index
- Assumes lists are parallel (same order)

**Lines 136-137: Fallback to name matching**

```csharp
if (prefab == null)
    prefab = buildingPrefabs.Find(p => p != null && p.name == data.displayName);
```

**What is `List.Find(predicate)`:**

- Searches list for first item matching condition
- `p => p != null && p.name == data.displayName` is a **lambda expression**
- Returns first prefab whose name matches displayName

**Lambda breakdown:**

```csharp
p => p != null && p.name == data.displayName
//  ↑        ↑
//  |        The condition to check for each item
//  Parameter: each item in the list

// Equivalent to:
bool MatchesName(GameObject p)
{
    return p != null && p.name == data.displayName;
}
```

---

### Lines 145-153: Calculate spawn position

```csharp
// Get spawn position
Vector3 worldPos;
if (spawnAtCenter)
    worldPos = GetScreenCenterWorldPosition(0f);
else
{
    worldPos = Camera.main != null ? Camera.main.ScreenToWorldPoint(Input.mousePosition) : Vector3.zero;
    worldPos.z = 0f;
}
```

**Ternary operator `? :`:**

```csharp
Camera.main != null ? Camera.main.ScreenToWorldPoint(Input.mousePosition) : Vector3.zero
//     condition    ?           if-true-value                           : if-false-value
```

**When spawnAtCenter is false:**

1. Get mouse position in screen coordinates: `Input.mousePosition`
2. Convert to world coordinates: `Camera.main.ScreenToWorldPoint()`
3. Set Z to 0 for 2D game

---

### Lines 155-159: Create the building

```csharp
// Spawn building
GameObject obj = Instantiate(prefab, placeRoot);
obj.transform.position = worldPos;
obj.transform.localScale = defaultSpawnScale;
obj.name = data.displayName;
```

**Line 156: `Instantiate(prefab, placeRoot)`**

- Creates copy of prefab
- Makes it child of placeRoot
- Returns the new GameObject

**Line 157: `obj.transform.position = worldPos;`**

- Sets world position (not local position)
- Building appears at mouse/center position

**Line 158: `obj.transform.localScale = defaultSpawnScale;`**

- Sets the scale relative to parent
- (1, 1) = normal size

---

### Lines 161-173: Setup building components

```csharp
var bi = obj.GetComponent<BuildingItem>();
if (bi != null) bi.Init(data);

var dr = obj.GetComponent<Draggable>();
if (dr == null) dr = obj.AddComponent<Draggable>();

currentObject = obj;
currentDraggable = dr;

dr.Init(gridSize, OnPlacementConfirmed, OnPlacementCanceled, this);

ShowConfirmPanel();
audioManager?.PlayClick();
```

**Line 161-162: Initialize BuildingItem**

- BuildingItem stores the building's data and cost
- Used later for refunds and identification

**Lines 164-165: Get or add Draggable**

```csharp
var dr = obj.GetComponent<Draggable>();
if (dr == null) dr = obj.AddComponent<Draggable>();
```

- First try to get existing Draggable component
- If prefab doesn't have one, add it dynamically
- This makes prefabs more flexible

**Lines 167-168: Store references**

```csharp
currentObject = obj;
currentDraggable = dr;
```

- Track what's being placed
- Used by Confirm/Cancel methods

**Line 170: Initialize Draggable**

```csharp
dr.Init(gridSize, OnPlacementConfirmed, OnPlacementCanceled, this);
```

Passes:

- `gridSize`: For snapping
- `OnPlacementConfirmed`: Callback when placement is done
- `OnPlacementCanceled`: Callback when placement is canceled
- `this`: Reference to VillageManager

---

## Lines 191-236: OnPlacementConfirmed Method

```csharp
public void OnPlacementConfirmed(GameObject placed)
{
    if (placed == null) placed = currentObject;

    if (placed == null)
    {
        Debug.LogWarning("VillageManager: No object to confirm.");
        HideConfirmPanel();
        return;
    }

    // Spend GreenPoints (100 GP)
    var bi = placed.GetComponent<BuildingItem>();
    if (bi != null && bi.data != null && GreenPointsManager.Instance != null)
    {
        int cost = bi.data.greenPointsCost;
        bool success = GreenPointsManager.Instance.TrySpend(cost);

        if (!success)
        {
            // Should not happen since we checked before spawning
            Debug.LogError("VillageManager: Failed to spend GP! This shouldn't happen.");
            ShowMessage($"Not enough GreenPoints!", Color.red);
            Destroy(placed);
            HideConfirmPanel();
            currentObject = null;
            currentDraggable = null;
            return;
        }

        // Show success message
        ShowMessage($"Built {bi.data.displayName}!\n-{cost} GreenPoints", Color.green);
        Debug.Log($"VillageManager: Built {bi.data.displayName} for {cost} GP");
    }

    var dr = placed.GetComponent<Draggable>();
    if (dr != null) dr.SetPlaced();

    HideConfirmPanel();

    currentObject = null;
    currentDraggable = null;

    saveManager?.SaveVillage();
    audioManager?.PlayPlace();
}
```

### What this method does:

1. Validate the placed object exists
2. Spend GreenPoints (deduct from player's balance)
3. Show success message
4. Mark building as "Placed" (no longer draggable)
5. Hide the confirmation panel
6. Clear tracking references
7. Save the village
8. Play placement sound

**Line 193: Fallback to currentObject**

```csharp
if (placed == null) placed = currentObject;
```

- Method can be called with null (from button click)
- Falls back to tracked object

**Lines 203-219: Spend GreenPoints**

**What is `TrySpend()`:**

- Attempts to deduct points
- Returns `true` if successful, `false` if not enough points
- Atomic operation: doesn't deduct if can't afford

**Double-check pattern:**

```csharp
if (!success)
{
    // Safety net - shouldn't happen normally
    // We checked affordability before spawning
}
```

**Line 227: `dr.SetPlaced();`**

- Changes Draggable mode from Placement to Placed
- Now clicking the building triggers deletion flow instead of dragging

---

## Lines 238-250: OnPlacementCanceled Method

```csharp
public void OnPlacementCanceled(GameObject canceled)
{
    if (canceled == null) canceled = currentObject;

    if (canceled != null) Destroy(canceled);

    HideConfirmPanel();

    currentObject = null;
    currentDraggable = null;

    audioManager?.PlayCancel();
}
```

**What this method does:**

1. Get the canceled object
2. Destroy it (remove from scene)
3. Hide confirmation panel
4. Clear references
5. Play cancel sound

**Important: No GreenPoints spent**

- We only checked affordability, didn't actually spend
- Canceling is "free" - no refund needed

---

## Lines 252-253: Expression-Bodied Members

```csharp
public void ConfirmCurrentPlacement() => OnPlacementConfirmed(currentObject);
public void CancelCurrentPlacement() => OnPlacementCanceled(currentObject);
```

**What is `=>`:**

- Expression-bodied member syntax (C# 6+)
- Shorthand for single-expression methods

**Equivalent to:**

```csharp
public void ConfirmCurrentPlacement()
{
    OnPlacementConfirmed(currentObject);
}
```

**Why have these methods:**

- Clean interface for UI buttons
- Buttons call these, which call the main logic methods
- Simplifies button listener setup

---

## Lines 255-266: OnPlacedObjectClicked Method

```csharp
public void OnPlacedObjectClicked(GameObject obj)
{
    Debug.Log("VillageManager: OnPlacedObjectClicked => " + (obj ? obj.name : "null"));
    if (deleteManager != null)
    {
        deleteManager.Select(obj);
    }
    else
    {
        Debug.LogWarning("VillageManager: deleteManager is null! Assign DeleteManager in Inspector.");
    }
}
```

**When this is called:**

- Draggable.OnPointerDown() calls this when mode is Placed
- Player clicked an already-placed building

**What it does:**

- Forwards the click to DeleteManager
- DeleteManager shows deletion confirmation popup

---

## Lines 271-283: ShowMessage Method

```csharp
public void ShowMessage(string text, Color color)
{
    if (messagePanel == null || messageText == null) return;

    if (messageCoroutine != null)
        StopCoroutine(messageCoroutine);

    messageText.text = text;
    messageText.color = color;
    messagePanel.SetActive(true);

    messageCoroutine = StartCoroutine(HideMessageAfterDelay());
}
```

**What this method does:**

1. Validate UI references exist
2. Stop any existing message timer
3. Set message text and color
4. Show the message panel
5. Start coroutine to hide after delay

**Why stop previous coroutine:**

- If message A is showing and message B triggers
- Without stopping: A's timer might hide B prematurely
- With stopping: B gets its full display time

---

## Lines 285-290: HideMessageAfterDelay Coroutine

```csharp
private IEnumerator HideMessageAfterDelay()
{
    yield return new WaitForSeconds(messageDuration);
    if (messagePanel != null)
        messagePanel.SetActive(false);
}
```

### What is a Coroutine:

- A method that can **pause and resume** execution
- Runs alongside Update() without blocking
- Uses `yield return` to pause

**`yield return new WaitForSeconds(messageDuration);`:**

- Pause for `messageDuration` seconds (default 2)
- Unity continues running other code
- After delay, execution resumes on next line

**Coroutine Timeline:**

```
Frame 1: ShowMessage() called → StartCoroutine() starts HideMessageAfterDelay()
Frame 1: yield return WaitForSeconds(2) → Coroutine pauses
Frame 2-60: Other code runs, message is visible
Frame 61 (after 2 sec): Coroutine resumes → messagePanel.SetActive(false)
```

---

## Lines 292-300: GetScreenCenterWorldPosition Method

```csharp
private Vector3 GetScreenCenterWorldPosition(float targetZ)
{
    var cam = Camera.main;
    if (cam == null) return Vector3.zero;
    Vector3 screenCenter = new Vector3(Screen.width / 2f, Screen.height / 2f, Mathf.Abs(cam.transform.position.z - targetZ));
    Vector3 world = cam.ScreenToWorldPoint(screenCenter);
    world.z = targetZ;
    return world;
}
```

**What this method does:**

- Calculates the world position of screen center
- Used when spawning buildings at center

**Line 296: Calculate screen center**

```csharp
Vector3 screenCenter = new Vector3(
    Screen.width / 2f,     // X = half of screen width
    Screen.height / 2f,    // Y = half of screen height
    Mathf.Abs(cam.transform.position.z - targetZ)  // Z = distance from camera
);
```

**Why the Z calculation:**

- ScreenToWorldPoint needs proper Z distance
- Gets absolute distance from camera to target Z plane
- Ensures accurate world position

---

# How Everything Works Together

## Complete Building Placement Flow

```
1. GAME START
   └── Start() runs
       ├── Hide confirm panel
       ├── Hide message panel
       ├── Setup button listeners
       └── PopulateInventory()
           └── Creates InventoryButton for each BuildingData

2. PLAYER CLICKS INVENTORY BUTTON
   └── InventoryButton.OnClickPlace()
       └── VillageManager.SpawnBuildingFromData(data)
           ├── Check if player can afford (GreenPointsManager.CanAfford)
           │   └── If NO: ShowMessage("Not enough GP!", red), return
           ├── Find matching prefab
           ├── Get spawn position (mouse or center)
           ├── Instantiate building prefab
           ├── Initialize BuildingItem and Draggable
           ├── Show confirm/cancel panel
           └── Play click sound

3. PLAYER DRAGS BUILDING
   └── Draggable.OnDrag()
       ├── Convert screen to world position
       ├── Snap to grid
       └── Update building position

4. PLAYER CLICKS CONFIRM BUTTON
   └── ConfirmCurrentPlacement()
       └── OnPlacementConfirmed(currentObject)
           ├── Spend GreenPoints (TrySpend)
           ├── Show success message
           ├── Set Draggable mode to Placed
           ├── Hide confirm panel
           ├── Save village
           └── Play place sound

5. (OR) PLAYER CLICKS CANCEL BUTTON
   └── CancelCurrentPlacement()
       └── OnPlacementCanceled(currentObject)
           ├── Destroy building
           ├── Hide confirm panel
           └── Play cancel sound

6. PLAYER CLICKS PLACED BUILDING
   └── Draggable.OnPointerDown() (mode = Placed)
       └── VillageManager.OnPlacedObjectClicked(obj)
           └── DeleteManager.Select(obj)
               └── Show deletion confirmation popup
```

---

# Common Mistakes & Notes

## Mistake 1: Lists not in sync

**Problem:** `allBuildingData` and `buildingPrefabs` have different lengths
**Result:** Some buildings have no prefab → Error
**Fix:** Ensure both lists have same number of items in same order

## Mistake 2: Missing prefab components

**Problem:** Building prefab missing SpriteRenderer or Collider
**Result:** Invisible building or can't click to delete
**Fix:** Ensure prefabs have all required components

## Mistake 3: Null Inspector references

**Problem:** Forgot to assign panelConfirmPlacement, buttons, etc.
**Result:** Features don't work, warnings in Console
**Fix:** Check Console for warnings, assign all fields in Inspector

## Mistake 4: Missing EventSystem

**Problem:** No EventSystem in scene
**Result:** Buttons don't respond to clicks
**Fix:** Add GameObject > UI > EventSystem

## Mistake 5: Button onClick already set in Editor

**Problem:** Button had listeners set in Inspector AND code adds more
**Result:** Method called multiple times per click
**Fix:** Always call `RemoveAllListeners()` before `AddListener()`

---

# Summary Table

| Method                           | Purpose                         | Called By                         |
| -------------------------------- | ------------------------------- | --------------------------------- |
| `Start()`                        | Initialize UI and inventory     | Unity (once at start)             |
| `PopulateInventory()`            | Generate inventory buttons      | Start(), can be called externally |
| `SpawnBuildingFromData()`        | Create new building             | InventoryButton                   |
| `OnPlacementConfirmed()`         | Finalize building placement     | Draggable, Confirm button         |
| `OnPlacementCanceled()`          | Cancel building placement       | Draggable, Cancel button          |
| `OnPlacedObjectClicked()`        | Handle click on placed building | Draggable                         |
| `ShowMessage()`                  | Display feedback to player      | Various internal methods          |
| `GetScreenCenterWorldPosition()` | Calculate center world coords   | SpawnBuildingFromData()           |
