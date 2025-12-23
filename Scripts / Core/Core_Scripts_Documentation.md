# 📚 Core Scripts - Complete Line-by-Line Documentation

This document provides **extremely detailed, line-by-line explanations** of all scripts in the `Assets/Scripts/Core` folder. Written for beginners who want to fully understand how Unity works internally.

---

# Table of Contents

1. [GreenPointsManager.cs](#-script-1-greenpointsmanagercs)
2. [SaveManager.cs](#-script-2-savemanagercs)
3. [AudioManager.cs](#-script-3-audiomanagercs)
4. [GreenPointsDisplay.cs](#-script-4-greenpointsdisplaycs)

---

# 📗 Script 1: GreenPointsManager.cs

**File Path:** `Assets/Scripts/Core/GreenPointsManager.cs`

---

## Script Purpose

This script is the **central currency manager** for the entire game. It is responsible for:

- Storing the player's current GreenPoints (the game's only currency)
- Adding points when the player does something correct
- Deducting points when the player makes mistakes
- Allowing other scripts to spend points (for building)
- Notifying the UI whenever points change via events
- Persisting across all scenes (never destroyed when changing scenes)

---

## Complete Script Code

```csharp
using System;
using UnityEngine;

/// <summary>
/// Unified GreenPoints Manager - Single resource system for the entire game
/// Manages all GreenPoints: earning, spending, events, and persistence
/// </summary>
public class GreenPointsManager : MonoBehaviour
{
    public static GreenPointsManager Instance { get; private set; }

    [Header("Starting Points")]
    [Tooltip("Initial GreenPoints for new players")]
    public int startingGreenPoints = 100;

    // Current GreenPoints
    private int _greenPoints = 0;
    public int GreenPoints => _greenPoints;

    // Events for UI updates
    public event Action<int> OnGreenPointsChanged;

    void Awake()
    {
        // Singleton pattern - persist across scenes
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);
            _greenPoints = startingGreenPoints;
            Debug.Log($"GreenPointsManager: Initialized with {_greenPoints} GP");
        }
        else
        {
            Destroy(gameObject);
        }
    }

    /// <summary>
    /// Add GreenPoints (for correct answers, rewards, etc.)
    /// </summary>
    public void AddPoints(int amount)
    {
        if (amount <= 0)
        {
            Debug.LogWarning($"GreenPointsManager: Tried to add non-positive amount: {amount}");
            return;
        }

        _greenPoints += amount;
        Debug.Log($"GreenPointsManager: +{amount} GP. Total: {_greenPoints}");
        OnGreenPointsChanged?.Invoke(_greenPoints);
    }

    /// <summary>
    /// Deduct GreenPoints (for wrong answers, penalties)
    /// Cannot go below 0
    /// </summary>
    public void DeductPoints(int amount)
    {
        if (amount <= 0) return;

        _greenPoints = Mathf.Max(0, _greenPoints - amount);
        Debug.Log($"GreenPointsManager: -{amount} GP. Total: {_greenPoints}");
        OnGreenPointsChanged?.Invoke(_greenPoints);
    }

    /// <summary>
    /// Try to spend GreenPoints (for building)
    /// Returns true if successful, false if not enough
    /// </summary>
    public bool TrySpend(int amount)
    {
        if (amount <= 0) return true;

        if (_greenPoints >= amount)
        {
            _greenPoints -= amount;
            Debug.Log($"GreenPointsManager: Spent {amount} GP. Remaining: {_greenPoints}");
            OnGreenPointsChanged?.Invoke(_greenPoints);
            return true;
        }

        Debug.LogWarning($"GreenPointsManager: Not enough GP! Need {amount}, have {_greenPoints}");
        return false;
    }

    /// <summary>
    /// Check if player can afford a cost
    /// </summary>
    public bool CanAfford(int amount)
    {
        return _greenPoints >= amount;
    }

    /// <summary>
    /// Set GreenPoints directly (for loading saved data)
    /// </summary>
    public void SetPoints(int amount)
    {
        _greenPoints = Mathf.Max(0, amount);
        Debug.Log($"GreenPointsManager: Set to {_greenPoints} GP");
        OnGreenPointsChanged?.Invoke(_greenPoints);
    }

    /// <summary>
    /// Reset to starting points
    /// </summary>
    public void ResetToDefault()
    {
        _greenPoints = startingGreenPoints;
        Debug.Log($"GreenPointsManager: Reset to {_greenPoints} GP");
        OnGreenPointsChanged?.Invoke(_greenPoints);
    }
}
```

---

## Imports / Using Statements

### Line 1: `using System;`

```csharp
using System;
```

**What it does:**

- This imports the core C# `System` namespace from the .NET framework
- The `System` namespace contains fundamental types like `Action`, `Func`, `Exception`, `Math`, etc.

**Why it's needed here:**

- This script uses `Action<int>` for the event system (line 21)
- `Action<int>` is a delegate type that represents a method that takes an `int` parameter and returns nothing
- Without this line, the compiler would not recognize `Action<int>`

**What would happen if removed:**

- Compilation error: `The type or namespace name 'Action' could not be found`

---

### Line 2: `using UnityEngine;`

```csharp
using UnityEngine;
```

**What it does:**

- This imports Unity's core engine namespace
- Contains ALL Unity-specific types: `MonoBehaviour`, `GameObject`, `Transform`, `Debug`, `Mathf`, etc.

**Why it's needed here:**

- This script inherits from `MonoBehaviour` (Unity component)
- Uses `Debug.Log()` for console output
- Uses `Mathf.Max()` for clamping values
- Uses `DontDestroyOnLoad()` Unity function

**What would happen if removed:**

- Compilation error: Almost every Unity-related type would be unrecognized

---

## Class Declaration

### Lines 8-9: Class Definition

```csharp
public class GreenPointsManager : MonoBehaviour
{
```

#### Keyword: `public`

**What it does:**

- Makes this class accessible from ANY other script in the project
- Other scripts can reference `GreenPointsManager` by name

**Why it's needed:**

- Other scripts (like mini-games, village manager, UI) need to access this manager to add/spend points

**What would happen if changed to `private`:**

- Classes cannot be private at namespace level - causes compilation error

---

#### Keyword: `class`

**What it does:**

- Declares a reference type (class) in C#
- Classes can contain fields, properties, methods, events

**Unity context:**

- Unity scripts are always classes (or structs for data)
- Each class typically corresponds to one component type

---

#### Name: `GreenPointsManager`

**What it does:**

- This is the unique identifier for this class
- Must match the filename exactly (GreenPointsManager.cs)

**Unity requirement:**

- Unity requires the class name to match the file name for MonoBehaviour scripts
- If they don't match, Unity will show a warning and the script won't work correctly

---

#### Inheritance: `: MonoBehaviour`

**What it does:**

- This class inherits from Unity's `MonoBehaviour` base class
- `MonoBehaviour` is the base class for all Unity components that can be attached to GameObjects

**What MonoBehaviour provides:**

- Lifecycle methods: `Awake()`, `Start()`, `Update()`, `FixedUpdate()`, `OnDestroy()`, etc.
- Access to the GameObject: `this.gameObject`
- Access to the Transform: `this.transform`
- Ability to be attached to GameObjects in the Inspector
- Coroutine support: `StartCoroutine()`
- Ability to be enabled/disabled

**Why it's needed:**

- Without inheriting from MonoBehaviour, this script cannot:
  - Be attached to a GameObject
  - Use Unity lifecycle methods like `Awake()`
  - Use `DontDestroyOnLoad()`

**What would happen if removed:**

- The script becomes a plain C# class
- Cannot attach to GameObjects
- No access to Unity lifecycle

---

## Variables & Fields

### Line 10: The Singleton Instance

```csharp
public static GreenPointsManager Instance { get; private set; }
```

**Breaking this down piece by piece:**

#### `public`

- The Instance can be accessed from ANY other script
- Other scripts will use `GreenPointsManager.Instance` to access this

#### `static`

- This property belongs to the CLASS itself, not to any specific object instance
- There is only ONE `Instance` shared across all uses
- Can be accessed without creating an object: `GreenPointsManager.Instance`

#### `GreenPointsManager`

- The return type is this class itself
- The property holds a reference to THE single instance of this manager

#### `Instance`

- The name of the property
- Convention: Singletons typically use `Instance` or `instance`

#### `{ get; private set; }`

- This is an auto-implemented property with different accessibilities:
  - `get` is public: Any script can READ the Instance
  - `set` is private: Only THIS class can ASSIGN the Instance
- This prevents other scripts from accidentally replacing the singleton

**Why this pattern exists (Singleton Pattern):**

- Ensures only ONE GreenPointsManager exists in the entire game
- Provides global access point: `GreenPointsManager.Instance.AddPoints(10)`

**What would happen if this was a regular field instead:**

```csharp
public static GreenPointsManager Instance;  // Anyone could set this to null!
```

- Other scripts could accidentally write: `GreenPointsManager.Instance = null;`
- The `private set` prevents this

---

### Lines 12-14: Starting Points Configuration

```csharp
[Header("Starting Points")]
[Tooltip("Initial GreenPoints for new players")]
public int startingGreenPoints = 100;
```

#### Line 12: `[Header("Starting Points")]`

**What it does:**

- This is an **Attribute** in C# (also called Decorator in other languages)
- `Header` is a Unity attribute that adds a bold header label in the Inspector

**In the Inspector:**

```
▼ Starting Points
   Starting Green Points: 100
```

**Why it's useful:**

- Organizes the Inspector when you have many variables
- Makes it clear which variables belong together

**What would happen if removed:**

- Script works identically
- Inspector just won't have the header text

---

#### Line 13: `[Tooltip("Initial GreenPoints for new players")]`

**What it does:**

- Another Unity attribute
- When you hover your mouse over this field in the Inspector, this text appears as a tooltip

**Why it's useful:**

- Documents what the variable does without needing external documentation
- Helps team members understand the purpose of each field

---

#### Line 14: `public int startingGreenPoints = 100;`

**Breaking this down:**

##### `public`

- This field is exposed to the Unity Inspector
- Can be edited in the Inspector when the script is attached to a GameObject
- Can also be accessed from other scripts

**Unity Inspector behavior with `public`:**

- Unity automatically shows PUBLIC fields in the Inspector
- The value set in the Inspector OVERRIDES the default value (100)
- This is part of Unity's serialization system

##### `int`

- Integer data type (whole numbers: -2,147,483,648 to 2,147,483,647)
- No decimal places

##### `startingGreenPoints`

- Variable name following camelCase convention
- Describes what it stores: the starting amount of green points

##### `= 100`

- Default value
- If the Inspector value is never changed, new players start with 100 GP
- This is a FALLBACK value; Inspector values take precedence

---

### Lines 16-18: Current Points Storage

```csharp
// Current GreenPoints
private int _greenPoints = 0;
public int GreenPoints => _greenPoints;
```

#### Line 17: `private int _greenPoints = 0;`

##### `private`

- This field can ONLY be accessed from within this class
- NOT visible in the Unity Inspector
- NOT accessible from other scripts directly

**Why private:**

- **Encapsulation**: We don't want other scripts to directly modify points
- Other scripts must use `AddPoints()` or `DeductPoints()` methods
- This ensures we always trigger the event when points change

##### `_greenPoints`

- The underscore prefix is a naming convention
- Convention: `_` prefix indicates a private backing field
- This is the ACTUAL storage location for the points value

##### `= 0`

- Initialized to 0 (will be set to `startingGreenPoints` in Awake)

---

#### Line 18: `public int GreenPoints => _greenPoints;`

**What it does:**

- This is a **read-only property** using expression-body syntax
- `=>` is the expression-body syntax (lambda arrow)
- It's equivalent to:

```csharp
public int GreenPoints
{
    get { return _greenPoints; }
}
```

**Why a property instead of public field:**

- Properties allow control over access
- This property has NO setter, so it's read-only
- Other scripts can READ points but NOT write directly

**How other scripts use it:**

```csharp
int currentPoints = GreenPointsManager.Instance.GreenPoints;  // Works!
GreenPointsManager.Instance.GreenPoints = 500;  // ERROR! No setter!
```

---

### Lines 20-21: Event Declaration

```csharp
// Events for UI updates
public event Action<int> OnGreenPointsChanged;
```

#### Line 21: `public event Action<int> OnGreenPointsChanged;`

##### `public`

- Other scripts can subscribe to this event
- Example: `GreenPointsManager.Instance.OnGreenPointsChanged += MyMethod;`

##### `event`

- This is a C# keyword that declares an event
- Events are a way to implement the **Observer Pattern**
- Events allow loose coupling between scripts

**What makes events special:**

- Outside scripts can only `+=` (subscribe) or `-=` (unsubscribe)
- Outside scripts CANNOT invoke/fire the event
- Only THIS class can invoke it: `OnGreenPointsChanged?.Invoke(value);`

##### `Action<int>`

- This is the **delegate type** for the event
- `Action<int>` means: "A method that takes one int parameter and returns void"
- Any method matching this signature can subscribe

**Examples of compatible methods:**

```csharp
void UpdateUI(int newPoints) { }           // ✓ Compatible
void HandlePointChange(int x) { }          // ✓ Compatible
void NoParameter() { }                      // ✗ Incompatible (no int)
int ReturnsSomething(int x) { return x; }  // ✗ Incompatible (returns int)
```

##### `OnGreenPointsChanged`

- Name of the event
- Convention: Events often start with "On" (OnDeath, OnClick, OnPointsChanged)

**How event subscription works:**

```csharp
// In another script (e.g., GreenPointsDisplay):
void Start() {
    // Subscribe: "Call my UpdateDisplay method whenever points change"
    GreenPointsManager.Instance.OnGreenPointsChanged += UpdateDisplay;
}

void UpdateDisplay(int newPoints) {
    myText.text = "Points: " + newPoints;
}

void OnDestroy() {
    // Unsubscribe: Prevent memory leaks and errors
    GreenPointsManager.Instance.OnGreenPointsChanged -= UpdateDisplay;
}
```

---

## Unity Lifecycle Methods

### Lines 23-37: Awake Method

```csharp
void Awake()
{
    // Singleton pattern - persist across scenes
    if (Instance == null)
    {
        Instance = this;
        DontDestroyOnLoad(gameObject);
        _greenPoints = startingGreenPoints;
        Debug.Log($"GreenPointsManager: Initialized with {_greenPoints} GP");
    }
    else
    {
        Destroy(gameObject);
    }
}
```

#### Line 23: `void Awake()`

**What it does:**

- `Awake()` is a Unity **lifecycle method** (also called Magic Method or Message)
- Unity automatically calls this method; you never call it yourself

**When Unity calls Awake():**

- Called **once** per script instance
- Called when the GameObject is first loaded (scene load or Instantiate)
- Called BEFORE `Start()` but AFTER the constructor
- Called even if the script component is disabled
- Called in a specific order based on Script Execution Order settings

**Execution Order:**

1. Constructor (rarely used in Unity)
2. **Awake()** ← This is called first of the lifecycle methods
3. OnEnable()
4. Start()
5. Update() (called every frame)

**Why Awake() instead of Start():**

- Awake() runs BEFORE Start()
- Use Awake() for initialization that other scripts might depend on in their Start()
- The Singleton setup MUST happen in Awake() so other scripts can access `Instance` in their Start()

---

#### Line 26: `if (Instance == null)`

**What it does:**

- Checks if a GreenPointsManager already exists
- `Instance` is initially `null` because static fields default to null
- If this is the FIRST GreenPointsManager to run Awake(), Instance will be null

---

#### Line 28: `Instance = this;`

**What it does:**

- Assigns THIS specific instance to the static `Instance` property
- `this` refers to the current object (this GreenPointsManager component)

**After this line:**

- Any script can access this manager via `GreenPointsManager.Instance`

---

#### Line 29: `DontDestroyOnLoad(gameObject);`

**What it does:**

- This is a Unity static method
- Tells Unity: "Do NOT destroy this GameObject when loading a new scene"

**Normal Unity behavior:**

- When you call `SceneManager.LoadScene("SomeScene")`, ALL GameObjects in the current scene are destroyed
- Any MonoBehaviour attached to them is also destroyed

**With DontDestroyOnLoad:**

- This specific GameObject survives scene changes
- The GreenPointsManager continues to exist in the next scene
- Player's points are preserved when switching scenes

**What `gameObject` refers to:**

- `gameObject` (lowercase 'g') is a property inherited from `MonoBehaviour`
- It returns the GameObject this script is attached to

---

#### Line 30: `_greenPoints = startingGreenPoints;`

**What it does:**

- Sets the private points storage to the starting value
- `_greenPoints` was initialized to 0, now it becomes 100 (or whatever Inspector value)

---

#### Lines 33-35: Else Block (Destroying Duplicates)

```csharp
else
{
    Destroy(gameObject);
}
```

**When this executes:**

- If `Instance` is NOT null
- Meaning: Another GreenPointsManager already exists

**What it does:**

- `Destroy(gameObject)` destroys this duplicate GameObject
- Only ONE GreenPointsManager ever exists

---

## Custom Functions

### Lines 42-52: AddPoints Method

```csharp
public void AddPoints(int amount)
{
    if (amount <= 0)
    {
        Debug.LogWarning($"GreenPointsManager: Tried to add non-positive amount: {amount}");
        return;
    }

    _greenPoints += amount;
    Debug.Log($"GreenPointsManager: +{amount} GP. Total: {_greenPoints}");
    OnGreenPointsChanged?.Invoke(_greenPoints);
}
```

#### Line 42: `public void AddPoints(int amount)`

- `public`: Other scripts CAN call this method
- `void`: Returns nothing
- `AddPoints`: Method name
- `(int amount)`: Requires one integer argument

---

#### Lines 44-48: Validation Guard Clause

```csharp
if (amount <= 0)
{
    Debug.LogWarning($"...");
    return;
}
```

**What it does:**

- Validates the input before proceeding
- Prevents adding 0 or negative points
- `return;` exits the method immediately (guard clause)

---

#### Line 50: `_greenPoints += amount;`

- Adds the amount to the private field
- `+=` is shorthand for `_greenPoints = _greenPoints + amount;`

---

#### Line 52: `OnGreenPointsChanged?.Invoke(_greenPoints);`

**This is the EVENT FIRING:**

- `OnGreenPointsChanged`: Our event
- `?.`: Null-conditional operator (only invoke if not null)
- `.Invoke(_greenPoints)`: Calls ALL subscribed methods with the new value

**What happens:**

- GreenPointsDisplay.UpdateDisplay(110) is called
- Any other subscribers are called...

---

### Lines 58-65: DeductPoints Method

```csharp
public void DeductPoints(int amount)
{
    if (amount <= 0) return;

    _greenPoints = Mathf.Max(0, _greenPoints - amount);
    Debug.Log($"GreenPointsManager: -{amount} GP. Total: {_greenPoints}");
    OnGreenPointsChanged?.Invoke(_greenPoints);
}
```

#### Line 63: `_greenPoints = Mathf.Max(0, _greenPoints - amount);`

**This is the key line:**

- `_greenPoints - amount`: Calculates new value
- `Mathf.Max(0, result)`: Returns whichever is LARGER (prevents negative points)
- Example: `Mathf.Max(0, -10)` returns 0

---

### Lines 71-85: TrySpend Method

```csharp
public bool TrySpend(int amount)
{
    if (amount <= 0) return true;

    if (_greenPoints >= amount)
    {
        _greenPoints -= amount;
        Debug.Log($"GreenPointsManager: Spent {amount} GP. Remaining: {_greenPoints}");
        OnGreenPointsChanged?.Invoke(_greenPoints);
        return true;
    }

    Debug.LogWarning($"GreenPointsManager: Not enough GP! Need {amount}, have {_greenPoints}");
    return false;
}
```

**Return type is `bool`:**

- Returns whether the spending was successful
- Caller uses this to know if purchase succeeded

**How the caller uses this:**

```csharp
if (GreenPointsManager.Instance.TrySpend(100))
{
    // Place the building
}
else
{
    // Show error: "Not enough GreenPoints!"
}
```

---

### Lines 90-93: CanAfford Method

```csharp
public bool CanAfford(int amount)
{
    return _greenPoints >= amount;
}
```

- Returns true if player has enough points
- Does NOT spend any points (read-only check)

---

### Lines 98-103: SetPoints Method

```csharp
public void SetPoints(int amount)
{
    _greenPoints = Mathf.Max(0, amount);
    Debug.Log($"GreenPointsManager: Set to {_greenPoints} GP");
    OnGreenPointsChanged?.Invoke(_greenPoints);
}
```

- Used when loading saved data
- REPLACES current value (doesn't add)

---

### Lines 108-113: ResetToDefault Method

```csharp
public void ResetToDefault()
{
    _greenPoints = startingGreenPoints;
    Debug.Log($"GreenPointsManager: Reset to {_greenPoints} GP");
    OnGreenPointsChanged?.Invoke(_greenPoints);
}
```

- Resets to starting value (100)
- Used for "New Game" functionality

---

## How Everything Works Together

### Step-by-Step Game Flow

1. **Game Starts (MainMenu scene loads)**

   - GreenPointsManager's `Awake()` runs
   - `Instance` is set to this manager
   - `DontDestroyOnLoad()` makes it persistent
   - Points set to 100

2. **UI Subscribes to Events**

   - GreenPointsDisplay's `Start()` runs
   - Subscribes: `OnGreenPointsChanged += UpdateDisplay`

3. **Player Answers Correctly in Mini-Game**

   - Mini-game calls: `GreenPointsManager.Instance.AddPoints(10)`
   - Points: 100 → 110
   - Event fires: `OnGreenPointsChanged.Invoke(110)`
   - UI automatically updates

4. **Player Builds Something (costs 100 GP)**
   - VillageManager checks: `CanAfford(100)` → true
   - `TrySpend(100)` → returns true, points: 10 GP
   - Building placed!

---

## Common Mistakes & Notes

### Mistake 1: Forgetting to Unsubscribe

```csharp
// BAD - Memory leak!
void Start() {
    GreenPointsManager.Instance.OnGreenPointsChanged += UpdateUI;
}
// Missing OnDestroy!
```

**Solution:**

```csharp
void OnDestroy() {
    if (GreenPointsManager.Instance != null)
        GreenPointsManager.Instance.OnGreenPointsChanged -= UpdateUI;
}
```

### Mistake 2: Accessing Instance Too Early

```csharp
// BAD - In a field initializer
int points = GreenPointsManager.Instance.GreenPoints;  // Instance might be null!
```

**Solution:** Access in Start() or later.

---

---

# 📗 Script 2: SaveManager.cs

**File Path:** `Assets/Scripts/Core/SaveManager.cs`

---

## Script Purpose

This script manages **persistent data storage**:

- Saves village buildings and their positions
- Saves GreenPoints
- Auto-saves every 20 seconds
- Saves when app quits or goes to background
- Loads saved data when returning to village
- Uses JSON format for save files

---

## Complete Script Code

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Reflection;
using UnityEngine;

public class SaveManager : MonoBehaviour
{
    [Header("References")]
    public VillageManager villageManager;
    public Transform placeRoot;
    public List<GameObject> buildingPrefabs;

    [Header("Auto Save")]
    public bool enableAutoSave = true;
    public float autoSaveInterval = 20f;
    private float autoSaveTimer = 0f;

    [Header("Settings")]
    public string fileName = "village_save.json";

    string SavePath => Path.Combine(Application.persistentDataPath, fileName);

    [Serializable]
    public class BuildingInfo
    {
        public string prefabName;
        public float x, y, z;
        public float rotZ;
        public float scaleX, scaleY;
        public int originalCost;
    }

    [Serializable]
    public class SaveFile
    {
        public List<BuildingInfo> buildings = new List<BuildingInfo>();
        public int greenPoints;
    }

    // ... (lifecycle methods and functions follow)
}
```

---

## Imports / Using Statements

### `using System;`

- For `Exception` class and `Serializable` attribute

### `using System.Collections.Generic;`

- For `List<T>` generic collection

### `using System.IO;`

- For `File.WriteAllText()`, `File.ReadAllText()`, `Path.Combine()`

### `using System.Reflection;`

- For `MethodInfo` (Unity version compatibility)

### `using UnityEngine;`

- Unity's core namespace

---

## Variables & Fields

### Lines 9-12: References

```csharp
[Header("References")]
public VillageManager villageManager;
public Transform placeRoot;
public List<GameObject> buildingPrefabs;
```

- `villageManager`: Reference to access building data
- `placeRoot`: Parent Transform containing all placed buildings
- `buildingPrefabs`: List of prefabs to match when loading

---

### Lines 14-17: Auto Save Configuration

```csharp
[Header("Auto Save")]
public bool enableAutoSave = true;
public float autoSaveInterval = 20f;
private float autoSaveTimer = 0f;
```

- `enableAutoSave`: Toggle auto-save on/off
- `autoSaveInterval`: Seconds between saves (20)
- `autoSaveTimer`: Private counter

---

### Line 22: Save Path Property

```csharp
string SavePath => Path.Combine(Application.persistentDataPath, fileName);
```

**What it does:**

- `Application.persistentDataPath`: OS-specific writable folder
  - Windows: `C:\Users\{Name}\AppData\LocalLow\{Company}\{Product}`
  - Android: `/data/data/{package}/files`
  - iOS: `/var/mobile/Containers/Data/Application/{guid}/Documents`
- `Path.Combine()`: Safely joins path parts with correct separators

---

### Lines 24-32: BuildingInfo Class

```csharp
[Serializable]
public class BuildingInfo
{
    public string prefabName;
    public float x, y, z;
    public float rotZ;
    public float scaleX, scaleY;
    public int originalCost;
}
```

**`[Serializable]`:**

- Required for `JsonUtility.ToJson()` to work
- Tells C# this class can be converted to/from data formats

**Fields store everything needed to recreate a building:**

- Position (x, y, z)
- Rotation (rotZ)
- Scale (scaleX, scaleY)
- Original cost (for accurate refund)

---

### Lines 34-38: SaveFile Class

```csharp
[Serializable]
public class SaveFile
{
    public List<BuildingInfo> buildings = new List<BuildingInfo>();
    public int greenPoints;
}
```

- Container for all save data
- `buildings`: List of all placed buildings
- `greenPoints`: Player's current points

---

## Key Methods

### Update Method (Auto-Save Timer)

```csharp
void Update()
{
    if (!enableAutoSave) return;
    autoSaveTimer += Time.deltaTime;

    if (autoSaveTimer >= autoSaveInterval)
    {
        autoSaveTimer = 0f;
        SaveVillage();
        Debug.Log("AutoSave: Village saved");
    }
}
```

**What happens:**

- Every frame, adds elapsed time to timer
- When timer reaches 20 seconds, saves and resets

---

### SaveVillage Method

```csharp
public void SaveVillage()
{
    SaveFile sf = new SaveFile();

    // Save buildings
    foreach (Transform t in placeRoot)
    {
        BuildingInfo bi = new BuildingInfo();
        bi.prefabName = NormalizePrefabName(go.name);
        bi.x = pos.x; bi.y = pos.y; bi.z = pos.z;
        bi.rotZ = go.transform.eulerAngles.z;
        bi.scaleX = go.transform.localScale.x;
        bi.scaleY = go.transform.localScale.y;
        bi.originalCost = buildingItem.originalCost;
        sf.buildings.Add(bi);
    }

    // Save GreenPoints
    sf.greenPoints = GreenPointsManager.Instance.GreenPoints;

    // Write to file
    string json = JsonUtility.ToJson(sf, true);
    File.WriteAllText(SavePath, json);
}
```

**Key points:**

- Iterates through all children of `placeRoot`
- Creates `BuildingInfo` for each building
- Uses `JsonUtility.ToJson()` to convert to JSON string
- `File.WriteAllText()` writes to disk

---

### LoadVillage Method

```csharp
public void LoadVillage()
{
    if (!File.Exists(SavePath)) return;

    string json = File.ReadAllText(SavePath);
    SaveFile sf = JsonUtility.FromJson<SaveFile>(json);

    // Clear existing buildings
    for (int i = placeRoot.childCount - 1; i >= 0; i--)
        DestroyImmediate(placeRoot.GetChild(i).gameObject);

    // Load GreenPoints
    GreenPointsManager.Instance.SetPoints(sf.greenPoints);

    // Recreate buildings
    foreach (var b in sf.buildings)
    {
        GameObject prefab = FindMatchingPrefab(b.prefabName);
        GameObject inst = Instantiate(prefab, placeRoot);
        inst.transform.position = new Vector3(b.x, b.y, b.z);
        // ... setup building ...
    }
}
```

**Key points:**

- Reads JSON from file
- Parses with `JsonUtility.FromJson<SaveFile>()`
- Clears existing buildings
- Restores GreenPoints
- Instantiates saved buildings

---

## Application Lifecycle Events

```csharp
void OnApplicationQuit()
{
    SaveVillage();
}

void OnApplicationPause(bool pause)
{
    if (pause) SaveVillage();
}
```

- `OnApplicationQuit()`: Called when game closes
- `OnApplicationPause(true)`: Called when app goes to background (mobile)

---

---

# 📗 Script 3: AudioManager.cs

**File Path:** `Assets/Scripts/Core/AudioManager.cs`

---

## Script Purpose

Manages all game audio:

- Background music (looping)
- Sound effects (one-shot)
- Singleton pattern for global access
- Persists across scenes

---

## Complete Script Code

```csharp
using UnityEngine;

public class AudioManager : MonoBehaviour
{
    public static AudioManager Instance;

    [Header("Sources")]
    public AudioSource musicSource;
    public AudioSource sfxSource;

    [Header("SFX Clips (optional)")]
    public AudioClip clickSound;
    public AudioClip placeSound;
    public AudioClip cancelSound;
    public AudioClip dragSound;

    void Awake()
    {
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
        }
    }

    public void PlayMusic(AudioClip clip, float volume = 1f)
    {
        if (clip == null || musicSource == null) return;
        musicSource.clip = clip;
        musicSource.volume = volume;
        musicSource.loop = true;
        musicSource.Play();
    }

    public void StopMusic()
    {
        if (musicSource == null) return;
        musicSource.Stop();
    }

    public void PlaySFX(AudioClip clip, float volume = 1f)
    {
        if (clip == null || sfxSource == null) return;
        sfxSource.PlayOneShot(clip, volume);
    }

    public void PlayClick() => PlaySFX(clickSound);
    public void PlayPlace() => PlaySFX(placeSound);
    public void PlayCancel() => PlaySFX(cancelSound);
    public void PlayDrag() => PlaySFX(dragSound);
}
```

---

## Variables & Fields

### Line 5: Singleton Instance

```csharp
public static AudioManager Instance;
```

- Static field for global access
- Simpler than property (no private set protection)

---

### Lines 7-9: Audio Sources

```csharp
[Header("Sources")]
public AudioSource musicSource;
public AudioSource sfxSource;
```

**What are AudioSources:**

- Unity components that play audio
- Must be attached to GameObjects
- `musicSource`: For background music (loops)
- `sfxSource`: For sound effects (one-shot)

**Why two sources:**

- Music and SFX need different behaviors
- Music loops, SFX plays once
- Can have different volumes

---

### Lines 11-15: Audio Clips

```csharp
[Header("SFX Clips (optional)")]
public AudioClip clickSound;
public AudioClip placeSound;
public AudioClip cancelSound;
public AudioClip dragSound;
```

- Pre-assigned common sound effects
- Drag audio files here in Inspector
- Optional - convenience methods use these

---

## Methods

### PlayMusic Method

```csharp
public void PlayMusic(AudioClip clip, float volume = 1f)
{
    if (clip == null || musicSource == null) return;
    musicSource.clip = clip;
    musicSource.volume = volume;
    musicSource.loop = true;
    musicSource.Play();
}
```

- `clip`: The audio file to play
- `volume = 1f`: Default parameter (optional)
- `loop = true`: Music repeats forever
- `.Play()`: Starts playback

---

### PlaySFX Method

```csharp
public void PlaySFX(AudioClip clip, float volume = 1f)
{
    if (clip == null || sfxSource == null) return;
    sfxSource.PlayOneShot(clip, volume);
}
```

**`PlayOneShot` vs `Play`:**

- `Play()`: Stops current audio, plays new
- `PlayOneShot()`: Plays on top of existing audio (overlapping)

**Why PlayOneShot for SFX:**

- Multiple sounds can play simultaneously
- Example: Rapid clicking doesn't cut off previous click sound

---

### Convenience Methods

```csharp
public void PlayClick() => PlaySFX(clickSound);
public void PlayPlace() => PlaySFX(placeSound);
public void PlayCancel() => PlaySFX(cancelSound);
public void PlayDrag() => PlaySFX(dragSound);
```

**Expression-body syntax (`=>`):**

- Shorthand for simple one-line methods
- `PlayClick()` is same as:

```csharp
public void PlayClick() { PlaySFX(clickSound); }
```

**Usage:**

```csharp
AudioManager.Instance?.PlayClick();  // Play click sound
```

---

---

# 📗 Script 4: GreenPointsDisplay.cs

**File Path:** `Assets/Scripts/Core/GreenPointsDisplay.cs`

---

## Script Purpose

Displays current GreenPoints in the UI:

- Subscribes to GreenPointsManager events
- Automatically updates when points change
- Simple UI component

---

## Complete Script Code

```csharp
using UnityEngine;
using TMPro;

public class GreenPointsDisplay : MonoBehaviour
{
    [Header("UI Reference")]
    [Tooltip("Text to display GreenPoints")]
    public TextMeshProUGUI greenPointsText;

    [Header("Display Settings")]
    [Tooltip("Format for the display")]
    public string displayFormat = "GreenPoints: {0}";

    void Start()
    {
        // Subscribe to GreenPoints changes
        if (GreenPointsManager.Instance != null)
        {
            GreenPointsManager.Instance.OnGreenPointsChanged += UpdateDisplay;

            // Initial update
            UpdateDisplay(GreenPointsManager.Instance.GreenPoints);
        }
        else
        {
            Debug.LogWarning("GreenPointsDisplay: GreenPointsManager not found!");
        }
    }

    void OnDestroy()
    {
        // Unsubscribe to prevent memory leaks
        if (GreenPointsManager.Instance != null)
        {
            GreenPointsManager.Instance.OnGreenPointsChanged -= UpdateDisplay;
        }
    }

    void UpdateDisplay(int greenPoints)
    {
        if (greenPointsText != null)
        {
            greenPointsText.text = string.Format(displayFormat, greenPoints.ToString("#,##0"));
        }
    }

    public void RefreshDisplay()
    {
        if (GreenPointsManager.Instance != null)
        {
            UpdateDisplay(GreenPointsManager.Instance.GreenPoints);
        }
    }
}
```

---

## Imports

### `using TMPro;`

```csharp
using TMPro;
```

- TextMeshPro namespace
- Required for `TextMeshProUGUI` class
- TextMeshPro is Unity's advanced text rendering package

---

## Variables

### Line 8: greenPointsText

```csharp
public TextMeshProUGUI greenPointsText;
```

**What is TextMeshProUGUI:**

- A UI text component (for Canvas)
- Better quality than legacy UI.Text
- Supports rich formatting

**In Inspector:**

- Drag a TextMeshPro text object here

---

### Line 12: displayFormat

```csharp
public string displayFormat = "GreenPoints: {0}";
```

- `{0}` is a placeholder for string.Format()
- When points are 150, displays: "GreenPoints: 150"

---

## Unity Lifecycle Methods

### Start Method

```csharp
void Start()
{
    if (GreenPointsManager.Instance != null)
    {
        GreenPointsManager.Instance.OnGreenPointsChanged += UpdateDisplay;
        UpdateDisplay(GreenPointsManager.Instance.GreenPoints);
    }
}
```

**Line-by-line:**

1. **Check if Instance exists**

   - Prevents null reference errors

2. **Subscribe to event**

   - `+= UpdateDisplay`: "Call UpdateDisplay when points change"

3. **Initial update**
   - Show current points immediately (don't wait for change)

---

### OnDestroy Method

```csharp
void OnDestroy()
{
    if (GreenPointsManager.Instance != null)
    {
        GreenPointsManager.Instance.OnGreenPointsChanged -= UpdateDisplay;
    }
}
```

**Why unsubscribe:**

- Prevents memory leaks
- Prevents errors when destroyed object is called
- `-= UpdateDisplay`: Remove our method from event

---

## Custom Methods

### UpdateDisplay Method

```csharp
void UpdateDisplay(int greenPoints)
{
    if (greenPointsText != null)
    {
        greenPointsText.text = string.Format(displayFormat, greenPoints.ToString("#,##0"));
    }
}
```

**When called:**

- When subscribed event fires
- Receives new point value as parameter

**`greenPoints.ToString("#,##0")`:**

- Formats with thousands separator
- 1000 → "1,000"
- 1000000 → "1,000,000"

**`string.Format(displayFormat, ...)`:**

- Replaces `{0}` with the formatted number
- Result: "GreenPoints: 1,000"

---

### RefreshDisplay Method

```csharp
public void RefreshDisplay()
{
    if (GreenPointsManager.Instance != null)
    {
        UpdateDisplay(GreenPointsManager.Instance.GreenPoints);
    }
}
```

- Public method for manual refresh
- Can be called from other scripts
- Useful for debugging

---

## How Everything Works Together

1. **Scene loads with GreenPointsDisplay**
2. **Start() runs:**
   - Subscribes to GreenPointsManager events
   - Shows initial points value
3. **Player earns/spends points:**
   - GreenPointsManager fires event
   - UpdateDisplay() automatically called
   - UI text updates
4. **Scene changes or object destroyed:**
   - OnDestroy() unsubscribes
   - No memory leaks

---

## Common Mistakes

### Mistake: Null TextMeshProUGUI

```csharp
// Inspector reference not set!
greenPointsText.text = "...";  // NullReferenceException!
```

**Solution:** Always check for null before using:

```csharp
if (greenPointsText != null)
{
    greenPointsText.text = "...";
}
```

### Mistake: Not unsubscribing

```csharp
// Forgetting OnDestroy causes:
// - Memory leaks
// - "MissingReferenceException" errors
```

---

# Summary

| Script             | Purpose          | Pattern            |
| ------------------ | ---------------- | ------------------ |
| GreenPointsManager | Central currency | Singleton + Events |
| SaveManager        | Persistence      | JSON serialization |
| AudioManager       | Sound playback   | Singleton          |
| GreenPointsDisplay | UI display       | Event subscriber   |

These core scripts work together to provide:

- ✅ Global currency management
- ✅ Persistent save/load
- ✅ Audio feedback
- ✅ Reactive UI updates
