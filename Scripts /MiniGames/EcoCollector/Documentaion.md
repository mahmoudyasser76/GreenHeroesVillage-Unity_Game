# 📚 EcoCollector Mini-Game - Complete Line-by-Line Documentation

**Folder Path:** `Assets/Scripts/MiniGames/EcoCollector`

This document provides an **extremely detailed, line-by-line explanation** of ALL scripts in the EcoCollector mini-game folder, teaching all Unity concepts from the ground up.

---

# Table of Contents

1. [Overview](#overview)
2. [Script 1: FallingItem.cs](#script-1-fallingitemcs)
3. [Script 2: EcoCollectorGame.cs](#script-2-ecocollectorgamecs)
4. [How Everything Works Together](#how-everything-works-together)

---

# Overview

The **Eco Collector** mini-game teaches recycling concepts:

- **Recyclable items** (plastic, paper, glass) fall from the sky
- **Non-recyclable items** also fall
- Players must **click only recyclable items** for +10 GreenPoints
- Clicking **non-recyclable items** penalizes -5 GreenPoints
- Game runs for 30 seconds (configurable)
- After game over, returns to Village scene

**Scripts in this folder:**

| Script                | Purpose                          |
| --------------------- | -------------------------------- |
| `FallingItem.cs`      | Individual falling item behavior |
| `EcoCollectorGame.cs` | Main game manager/controller     |

---

# Script 1: FallingItem.cs

**File Path:** `Assets/Scripts/MiniGames/EcoCollector/FallingItem.cs`

## Script Purpose

The `FallingItem` script controls **individual falling items**. Each item:

1. Falls from top of screen to bottom
2. Can be clicked by the player
3. Notifies the game manager when clicked
4. Destroys itself when clicked or falls off-screen

---

## Complete Script Code

```csharp
using UnityEngine;

public enum ItemType
{
    Recyclable,
    NonRecyclable
}

public class FallingItem : MonoBehaviour
{
    [Header("Item Properties")]
    public ItemType itemType;

    [Header("Visual")]
    public SpriteRenderer spriteRenderer;

    private float fallSpeed = 2f;
    private EcoCollectorGame gameManager;
    private bool collected = false;

    public void Initialize(float speed, EcoCollectorGame manager, Sprite itemSprite, ItemType type)
    {
        // ...
    }

    void Update()
    {
        // ...
    }

    void OnMouseDown()
    {
        // ...
    }

    void Collect()
    {
        // ...
    }
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

#### What it does:

- Imports Unity's core engine namespace
- Contains ALL fundamental Unity types and functionality

#### What it provides (used in this script):

| Type             | Purpose in this script                   |
| ---------------- | ---------------------------------------- |
| `MonoBehaviour`  | Base class for this component            |
| `GameObject`     | The object this script is attached to    |
| `Transform`      | Position/movement access via `transform` |
| `Vector3`        | 3D position and direction                |
| `Time`           | Frame timing with `Time.deltaTime`       |
| `Sprite`         | Visual image for the item                |
| `SpriteRenderer` | Component that renders sprites           |
| `Debug`          | Console logging                          |

#### What would happen if removed:

- **Compilation error:** Every Unity type would be unrecognized
- The script could not function at all

---

## Enum Declaration

### Lines 6-10: ItemType Enum

```csharp
public enum ItemType
{
    Recyclable,     // Recycable
    NonRecyclable   // Non Recycable
}
```

#### What is an Enum:

- Short for "enumeration"
- A type with a fixed set of named constants
- Each value is internally represented as an integer (0, 1, 2...)

#### Breaking it down:

**`public`:**

- This enum can be used by ANY script in the project
- Other scripts can declare variables of type `ItemType`

**`enum`:**

- Keyword to declare an enumeration type
- Creates a new custom type

**`ItemType`:**

- The name of this enum type
- Used to declare variables: `ItemType myType;`

**`Recyclable` and `NonRecyclable`:**

- The possible values this type can hold
- Internally: `Recyclable = 0`, `NonRecyclable = 1`

#### Why use an Enum here:

| Approach                     | Problem                          |
| ---------------------------- | -------------------------------- |
| `string type = "recyclable"` | Typos cause bugs: `"recylable"`  |
| `int type = 0`               | Magic numbers, unclear meaning   |
| `bool isRecyclable`          | Works but less extensible        |
| **`ItemType type`**          | **Clear, type-safe, extensible** |

#### Example usage:

```csharp
ItemType myItem = ItemType.Recyclable;

if (myItem == ItemType.Recyclable)
{
    // Award points
}
```

#### What would happen if removed:

- **Compilation error:** All references to `ItemType` would fail
- Need another way to distinguish item types

---

## Class Declaration

### Line 17: Class Definition

```csharp
public class FallingItem : MonoBehaviour
```

#### Breaking this down:

**`public`:**

- Class is accessible from any other script
- Can be added as a component via code: `AddComponent<FallingItem>()`

**`class`:**

- Declares a reference type in C#
- Can contain fields, properties, methods

**`FallingItem`:**

- The unique identifier for this class
- **Must match filename exactly:** `FallingItem.cs`
- Descriptive name: items that "fall"

**`: MonoBehaviour`:**

- Inherits from Unity's `MonoBehaviour` base class
- Required to attach to GameObjects
- Provides lifecycle methods, component access, etc.

---

## Variables & Fields

### Lines 19-20: Item Properties Header

```csharp
[Header("Item Properties")]
public ItemType itemType;
```

#### Line 19: `[Header("Item Properties")]`

**What it does:**

- Creates a labeled header/divider in the Unity Inspector
- Groups related fields under a readable section

**In the Inspector:**

```
▼ Item Properties
   Item Type: Recyclable ▼  (dropdown)
```

#### Line 20: `public ItemType itemType;`

**What it does:**

- Stores what type this item is (Recyclable or NonRecyclable)
- Set by `EcoCollectorGame` when spawning via `Initialize()`

**Why public:**

- Must be set from `EcoCollectorGame.SpawnItem()`
- Visible in Inspector for debugging
- Passed to game manager when clicked

**In Inspector:**

- Shows as dropdown with options: "Recyclable", "NonRecyclable"

**How it affects gameplay:**

- Determines whether clicking gives points (+10) or penalty (-5)

---

### Lines 22-23: Visual Header

```csharp
[Header("Visual")]
public SpriteRenderer spriteRenderer;
```

#### Line 23: `public SpriteRenderer spriteRenderer;`

**What is SpriteRenderer:**

- A Unity component that displays 2D images (sprites)
- Attached to the same GameObject
- The visual representation of the falling item

**What it does:**

- Reference to this item's SpriteRenderer component
- Used to change the sprite image dynamically

**Why public:**

- Can be assigned in Inspector (recommended)
- Or assigned via code with `GetComponent<SpriteRenderer>()`

**How it's used:**

```csharp
spriteRenderer.sprite = itemSprite;  // Change the displayed image
```

---

### Lines 25-27: Private Fields

```csharp
private float fallSpeed = 2f;
private EcoCollectorGame gameManager;
private bool collected = false;
```

#### Line 25: `private float fallSpeed = 2f;`

**What it does:**

- Stores how fast this item falls (units per second)
- Default value of 2.0 units/second

**Why private:**

- Set via `Initialize()` method, not Inspector
- Internal implementation detail

**How it's used:**

```csharp
transform.position += Vector3.down * fallSpeed * Time.deltaTime;
// Moves down by (fallSpeed * deltaTime) units each frame
```

**Example with fallSpeed = 2:**

```
Frame 1 (deltaTime = 0.016s): Move down 0.032 units
Frame 2 (deltaTime = 0.016s): Move down 0.032 units
After 1 second: Moved down ~2 units total
```

---

#### Line 26: `private EcoCollectorGame gameManager;`

**What it does:**

- Reference to the main game controller
- Used to notify when this item is clicked

**Why store reference:**

- Items need to report clicks to game manager
- Game manager tracks score and applies points

**Set via:**

```csharp
// In Initialize():
gameManager = manager;
```

**Used in:**

```csharp
// In Collect():
gameManager.OnItemClicked(itemType);
```

---

#### Line 27: `private bool collected = false;`

**What it does:**

- Flag to track if this item has already been collected
- Prevents double-collection

**Why needed:**

Without this flag:

```
Frame 1: Player clicks → OnMouseDown() → Collect() → score +10
Frame 1: Still processing → OnMouseDown() triggered again??? → score +20???
```

With this flag:

```
Frame 1: Player clicks → OnMouseDown() → collected = true → Collect() → score +10
Frame 1: If somehow triggered again → if (collected) return; → No double score
```

**Default value `= false`:**

- Item starts as "not collected"
- Set to `true` immediately when clicked

---

## Custom Functions

### Lines 32-45: Initialize Method

```csharp
public void Initialize(float speed, EcoCollectorGame manager, Sprite itemSprite, ItemType type)
{
    fallSpeed = speed;
    gameManager = manager;
    itemType = type;

    // Set sprite if available
    if (spriteRenderer != null && itemSprite != null)
    {
        spriteRenderer.sprite = itemSprite;
    }

    Debug.Log($"Item spawned: {itemType}");
}
```

#### Line 32: Method Signature

```csharp
public void Initialize(float speed, EcoCollectorGame manager, Sprite itemSprite, ItemType type)
```

**Parameters:**

| Parameter    | Type               | Purpose                      |
| ------------ | ------------------ | ---------------------------- |
| `speed`      | `float`            | How fast this item falls     |
| `manager`    | `EcoCollectorGame` | Reference to game controller |
| `itemSprite` | `Sprite`           | The visual image to display  |
| `type`       | `ItemType`         | Recyclable or NonRecyclable  |

**Why this method exists:**

Instead of setting each field individually:

```csharp
// BAD: Verbose and error-prone
var item = Instantiate(prefab);
var fi = item.GetComponent<FallingItem>();
fi.fallSpeed = 2f;
fi.gameManager = this;
fi.itemType = ItemType.Recyclable;
fi.spriteRenderer.sprite = someSprite;
```

Use a single initialization call:

```csharp
// GOOD: Clean and encapsulated
var fi = item.GetComponent<FallingItem>();
fi.Initialize(2f, this, someSprite, ItemType.Recyclable);
```

---

#### Lines 34-36: Store Parameters

```csharp
fallSpeed = speed;
gameManager = manager;
itemType = type;
```

**What it does:**

- Stores the passed parameters in the private fields
- Sets up this item's behavior

---

#### Lines 39-42: Set Sprite

```csharp
if (spriteRenderer != null && itemSprite != null)
{
    spriteRenderer.sprite = itemSprite;
}
```

**What it does:**

- Changes the visual appearance of this item
- Shows plastic bottle, paper, glass, etc.

**Null checks:**

- `spriteRenderer != null`: Ensures component exists
- `itemSprite != null`: Ensures a sprite was provided

**What is `.sprite`:**

- Property of SpriteRenderer component
- Setting it changes the displayed image

---

#### Line 44: Debug Log

```csharp
Debug.Log($"Item spawned: {itemType}");
```

**What it does:**

- Prints to the Console: "Item spawned: Recyclable" or "Item spawned: NonRecyclable"
- Helpful for debugging spawn logic

**String interpolation `$"...{variable}..."`:**

- Embeds variable value into the string
- `$"Item spawned: {itemType}"` → `"Item spawned: Recyclable"`

---

## Unity Lifecycle Methods

### Lines 47-59: Update Method

```csharp
void Update()
{
    if (collected) return;

    // Fall down
    transform.position += Vector3.down * fallSpeed * Time.deltaTime;

    // Destroy if falls off screen
    if (transform.position.y < -6f)
    {
        Destroy(gameObject);
    }
}
```

#### When Unity Calls Update():

- **Every single frame** while the GameObject is active
- After all `Start()` methods have completed
- 60 times per second at 60 FPS, 30 at 30 FPS, etc.

#### Line 49: Guard Clause

```csharp
if (collected) return;
```

**What it does:**

- If item was already collected, exit immediately
- Prevents movement after collection

**Why needed:**

- Brief delay between `collected = true` and `Destroy()`
- Item shouldn't move during that time

---

#### Line 52: Falling Movement

```csharp
transform.position += Vector3.down * fallSpeed * Time.deltaTime;
```

**Breaking this down:**

| Part               | Value                   | Meaning                 |
| ------------------ | ----------------------- | ----------------------- |
| `transform`        | This object's Transform | Access to position      |
| `.position`        | `Vector3`               | Current world position  |
| `+=`               |                         | Add to current value    |
| `Vector3.down`     | `(0, -1, 0)`            | Direction pointing down |
| `* fallSpeed`      | e.g., `* 2`             | Speed multiplier        |
| `* Time.deltaTime` | e.g., `* 0.016`         | Frame-rate independence |

**What is Time.deltaTime:**

- Time (in seconds) since last frame
- At 60 FPS: ~0.0167 seconds
- At 30 FPS: ~0.0333 seconds

**Why multiply by Time.deltaTime:**

Without deltaTime (BAD):

```
60 FPS: 60 frames/sec × 2 units/frame = 120 units/second ← TOO FAST
30 FPS: 30 frames/sec × 2 units/frame = 60 units/second ← DIFFERENT SPEED
```

With deltaTime (GOOD):

```
60 FPS: 60 frames × (2 × 0.0167) = 2 units/second ✓
30 FPS: 30 frames × (2 × 0.0333) = 2 units/second ✓
```

**Result:** Same speed regardless of frame rate!

---

#### Lines 55-58: Off-Screen Destruction

```csharp
if (transform.position.y < -6f)
{
    Destroy(gameObject);
}
```

**What it does:**

- Checks if item has fallen below y = -6 (off-screen)
- If so, destroys the item GameObject

**Why destroy off-screen items:**

- **Memory management:** Don't keep unused objects in memory
- **Performance:** Fewer objects = faster game
- **Cleanup:** Items that escaped should disappear

**What is `Destroy(gameObject)`:**

- `Destroy()` = Unity function to remove objects
- `gameObject` = This component's GameObject
- Removes the entire object (all components, children)

**What happens after Destroy:**

- Object is marked for destruction
- Removed at end of current frame
- Update() won't be called again

---

### Lines 61-67: OnMouseDown Method

```csharp
void OnMouseDown()
{
    if (collected) return;

    // Player clicked this item
    Collect();
}
```

#### What is OnMouseDown():

- A special Unity **callback method**
- Called when player clicks (mouse) or touches (mobile) this object

#### Requirements for OnMouseDown to work:

1. **Collider component** on the GameObject (BoxCollider2D, CircleCollider2D, etc.)
2. **Script with OnMouseDown()** on same GameObject
3. **No UI element blocking** the click

#### Line 63: Guard Clause

```csharp
if (collected) return;
```

**What it does:**

- If already collected, ignore the click
- Prevents double-collection within same frame

---

#### Line 66: Call Collect

```csharp
Collect();
```

**What it does:**

- Calls the `Collect()` method to process the click
- Separates "detect click" from "handle click"

**Why separate method:**

- `OnMouseDown` = Unity's entry point (detection)
- `Collect()` = Our logic (processing)
- Clean separation of concerns

---

### Lines 72-87: Collect Method

```csharp
void Collect()
{
    collected = true;

    // Notify game manager with item type
    if (gameManager != null)
    {
        gameManager.OnItemClicked(itemType);
    }

    // Visual feedback (optional)
    // TODO: Add particle effect or animation

    // Destroy the item
    Destroy(gameObject);
}
```

#### Line 74: Set Collected Flag

```csharp
collected = true;
```

**What it does:**

- Marks item as collected IMMEDIATELY
- First line in method = prevents race conditions

**Why first:**

- Prevents any possibility of double-collection
- Even if Update() somehow runs, it will exit early

---

#### Lines 77-80: Notify Game Manager

```csharp
if (gameManager != null)
{
    gameManager.OnItemClicked(itemType);
}
```

**What it does:**

- Tells the game manager this item was clicked
- Passes the item type so manager knows if correct/wrong

**The flow:**

```
Player clicks → OnMouseDown() → Collect() → gameManager.OnItemClicked(Recyclable)
                                                      ↓
                              EcoCollectorGame scores +10 GP
```

---

#### Lines 82-83: TODO Comment

```csharp
// Visual feedback (optional)
// TODO: Add particle effect or animation
```

**What this is:**

- A reminder for future enhancement
- Could add: explosion particles, fade animation, sound

---

#### Line 86: Destroy Item

```csharp
Destroy(gameObject);
```

**What it does:**

- Removes this item from the game
- Item disappears after being clicked

---

---

# Script 2: EcoCollectorGame.cs

**File Path:** `Assets/Scripts/MiniGames/EcoCollector/EcoCollectorGame.cs`

## Script Purpose

The `EcoCollectorGame` script is the **main controller** for the Eco Collector mini-game. It manages:

1. **Game timing** - 30-second countdown
2. **Item spawning** - Creates falling items at intervals
3. **Scoring** - Tracks correct/wrong clicks and GreenPoints
4. **UI updates** - Timer, score, game over panel
5. **Scene flow** - Returns to Village after game over

---

## Complete Script Code

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;
using TMPro;

public class EcoCollectorGame : MonoBehaviour
{
    [Header("Game Settings")]
    public float gameDuration = 30f;
    public float spawnInterval = 1f;
    public float fallSpeed = 2f;

    [Header("Spawn Area")]
    public float spawnMinX = -8f;
    public float spawnMaxX = 8f;
    public float spawnY = 6f;

    [Header("Prefabs")]
    public GameObject itemPrefab;

    [Header("Item Sprites")]
    public Sprite[] recyclableSprites;
    public Sprite[] nonRecyclableSprites;

    [Header("UI References")]
    public TextMeshProUGUI timerText;
    public TextMeshProUGUI scoreText;
    public TextMeshProUGUI greenPointsText;
    public GameObject gameOverPanel;
    public TextMeshProUGUI finalScoreText;
    public TextMeshProUGUI rewardText;

    [Header("Scoring - GreenPoints")]
    public int pointsPerRecyclable = 10;
    public int penaltyPerNonRecyclable = 5;

    // Game state
    private float timeRemaining;
    private int correctClicks = 0;
    private int wrongClicks = 0;
    private bool gameActive = false;
    private float spawnTimer = 0f;
    private int totalEarnedGP = 0;

    void Start() { /* ... */ }
    void Update() { /* ... */ }
    // ... more methods
}
```

---

## Imports / Using Statements

### Line 1: `using System.Collections;`

```csharp
using System.Collections;
```

#### What it does:

- Imports the base Collections namespace
- Contains `IEnumerator` interface for coroutines

#### Why it's here:

- **Not actually used** in the current code
- May have been used previously or planned for coroutines
- Could be removed without effect

---

### Line 2: `using UnityEngine;`

```csharp
using UnityEngine;
```

#### What it provides (used in this script):

| Type            | Purpose              |
| --------------- | -------------------- |
| `MonoBehaviour` | Base class           |
| `GameObject`    | Prefabs, panels      |
| `Vector3`       | Spawn positions      |
| `Quaternion`    | Rotation (identity)  |
| `Random`        | Random spawning      |
| `Time`          | deltaTime for timing |
| `Mathf`         | CeilToInt for timer  |
| `Sprite[]`      | Item images          |
| `Debug`         | Console logging      |

---

### Line 3: `using UnityEngine.SceneManagement;`

```csharp
using UnityEngine.SceneManagement;
```

#### What it does:

- Imports scene management functionality
- Used to load the Village scene after game over

#### What it provides:

```csharp
SceneManager.LoadScene("Village");  // Navigate to Village scene
SceneManager.GetActiveScene().name; // Get current scene name
```

---

### Line 4: `using TMPro;`

```csharp
using TMPro;
```

#### What it does:

- Imports TextMeshPro namespace
- High-quality text rendering for UI

#### What it provides:

- `TextMeshProUGUI` - UI text component

---

## Class Declaration

### Line 11: Class Definition

```csharp
public class EcoCollectorGame : MonoBehaviour
```

Same pattern as FallingItem:

- `public` = accessible everywhere
- `class` = reference type
- `EcoCollectorGame` = filename must match
- `: MonoBehaviour` = Unity component

---

## Variables & Fields

### Lines 13-16: Game Settings

```csharp
[Header("Game Settings")]
public float gameDuration = 30f;
public float spawnInterval = 1f;
public float fallSpeed = 2f;
```

#### Line 14: `public float gameDuration = 30f;`

**What it does:**

- How long the game lasts in seconds
- Default: 30 seconds

**In Inspector:**

- Editable field labeled "Game Duration"
- Designer can change without code

#### Line 15: `public float spawnInterval = 1f;`

**What it does:**

- Time between item spawns in seconds
- Default: 1 item per second

**Lower value = More items = Harder game**

#### Line 16: `public float fallSpeed = 2f;`

**What it does:**

- How fast items fall (units/second)
- Passed to each `FallingItem.Initialize()`

---

### Lines 18-21: Spawn Area

```csharp
[Header("Spawn Area")]
public float spawnMinX = -8f;
public float spawnMaxX = 8f;
public float spawnY = 6f;
```

**What these define:**

```
Screen Layout:
                    spawnY = 6 (top, off-screen)
                         ↓
    spawnMinX = -8  ←——————→  spawnMaxX = 8
           |                      |
           ▼                      ▼
     [Items spawn randomly between these X values]
```

**Line 21: `public float spawnY = 6f;`**

- Y position where items spawn
- Above visible screen so items "appear" from top

---

### Lines 23-24: Prefabs

```csharp
[Header("Prefabs")]
public GameObject itemPrefab;
```

#### What it does:

- The template for falling items
- Instantiated each time an item spawns

#### What the prefab needs:

- `FallingItem` script
- `SpriteRenderer` component
- `Collider2D` component (for click detection)

---

### Lines 26-31: Item Sprites

```csharp
[Header("Item Sprites")]
[Tooltip("Sprites for recycable items (Plastic, Paper, Glass))")]
public Sprite[] recyclableSprites;

[Tooltip("Sprites for unrecycable items)")]
public Sprite[] nonRecyclableSprites;
```

#### Line 28: `public Sprite[] recyclableSprites;`

**What is `Sprite[]`:**

- An array of Sprite objects
- Can hold multiple images

**What it does:**

- Contains images for recyclable items
- Random one chosen when spawning

**In Inspector:**

```
▼ Recyclable Sprites
   Size: 3
   Element 0: PlasticBottle (Sprite)
   Element 1: Paper (Sprite)
   Element 2: GlassBottle (Sprite)
```

---

### Lines 33-39: UI References

```csharp
[Header("UI References")]
public TextMeshProUGUI timerText;
public TextMeshProUGUI scoreText;
public TextMeshProUGUI greenPointsText;
public GameObject gameOverPanel;
public TextMeshProUGUI finalScoreText;
public TextMeshProUGUI rewardText;
```

| Field             | Purpose                         |
| ----------------- | ------------------------------- | --------- |
| `timerText`       | Shows "Time: 25s" countdown     |
| `scoreText`       | Shows "Correct: 5               | Wrong: 2" |
| `greenPointsText` | Shows current total GreenPoints |
| `gameOverPanel`   | Panel shown when game ends      |
| `finalScoreText`  | Shows final recycled count      |
| `rewardText`      | Shows total GP earned/lost      |

---

### Lines 41-46: Scoring

```csharp
[Header("Scoring - GreenPoints")]
[Tooltip("+10 GP for recycable items")]
public int pointsPerRecyclable = 10;

[Tooltip("-5 GP for non-recycable items")]
public int penaltyPerNonRecyclable = 5;
```

**Gameplay balance:**

- Correct click: +10 GP (reward)
- Wrong click: -5 GP (penalty)
- Net positive if player is more right than wrong

---

### Lines 48-54: Private Game State

```csharp
// Game state
private float timeRemaining;
private int correctClicks = 0;
private int wrongClicks = 0;
private bool gameActive = false;
private float spawnTimer = 0f;
private int totalEarnedGP = 0;
```

| Variable        | Purpose                                 |
| --------------- | --------------------------------------- |
| `timeRemaining` | Countdown timer (decreases each frame)  |
| `correctClicks` | Count of recyclable items clicked       |
| `wrongClicks`   | Count of non-recyclable items clicked   |
| `gameActive`    | Whether game is running                 |
| `spawnTimer`    | Accumulator for spawn timing            |
| `totalEarnedGP` | Running total of GP earned this session |

---

## Unity Lifecycle Methods

### Lines 56-59: Start Method

```csharp
void Start()
{
    StartGame();
}
```

#### When Unity Calls Start():

- Once when the scene loads and this component is enabled
- After all `Awake()` methods complete

#### What it does:

- Immediately starts the game
- No menu, no waiting - game begins on scene load

---

### Lines 61-80: Update Method

```csharp
void Update()
{
    if (!gameActive) return;

    timeRemaining -= Time.deltaTime;
    UpdateTimerUI();

    if (timeRemaining <= 0)
    {
        EndGame();
        return;
    }

    spawnTimer += Time.deltaTime;
    if (spawnTimer >= spawnInterval)
    {
        SpawnItem();
        spawnTimer = 0f;
    }
}
```

#### Line 63: Guard Clause

```csharp
if (!gameActive) return;
```

**What it does:**

- If game is not active, do nothing
- Prevents updates before game starts or after game ends

---

#### Lines 65-66: Timer Countdown

```csharp
timeRemaining -= Time.deltaTime;
UpdateTimerUI();
```

**How countdown works:**

```
Frame 1: timeRemaining = 30.0 - 0.016 = 29.984
Frame 2: timeRemaining = 29.984 - 0.017 = 29.967
...
After 30 seconds: timeRemaining ≈ 0
```

---

#### Lines 68-72: Check Game Over

```csharp
if (timeRemaining <= 0)
{
    EndGame();
    return;
}
```

**What it does:**

- When timer reaches zero, end the game
- `return` exits Update early (no more spawning)

---

#### Lines 74-79: Spawn Timer

```csharp
spawnTimer += Time.deltaTime;
if (spawnTimer >= spawnInterval)
{
    SpawnItem();
    spawnTimer = 0f;
}
```

**How spawn timing works:**

With `spawnInterval = 1.0`:

```
Frame 1: spawnTimer = 0 + 0.016 = 0.016
Frame 2: spawnTimer = 0.016 + 0.016 = 0.032
...
Frame 60: spawnTimer = 0.992 + 0.016 = 1.008 >= 1.0 → SpawnItem()!
         spawnTimer reset to 0
Frame 61: spawnTimer = 0 + 0.016 = 0.016
...
```

Result: One item spawns approximately every 1 second.

---

## Custom Functions

### Lines 82-97: StartGame Method

```csharp
void StartGame()
{
    timeRemaining = gameDuration;
    correctClicks = 0;
    wrongClicks = 0;
    totalEarnedGP = 0;
    gameActive = true;

    if (gameOverPanel != null)
        gameOverPanel.SetActive(false);

    UpdateScoreUI();
    UpdateTimerUI();

    Debug.Log("Eco Collector Started! Click Recyclable items only!");
}
```

**What it does:**

1. Set timer to full duration (30 seconds)
2. Reset all counters to zero
3. Activate the game
4. Hide game over panel (in case restarting)
5. Initialize UI display
6. Log start message

---

### Lines 99-131: SpawnItem Method

```csharp
void SpawnItem()
{
    if (itemPrefab == null) return;

    // randomize between recyclable and non-recyclable
    ItemType randomType = (Random.value > 0.5f) ? ItemType.Recyclable : ItemType.NonRecyclable;

    Sprite selectedSprite = null;

    if (randomType == ItemType.Recyclable)
    {
        if (recyclableSprites != null && recyclableSprites.Length > 0)
        {
            selectedSprite = recyclableSprites[Random.Range(0, recyclableSprites.Length)];
        }
    }
    else
    {
        if (nonRecyclableSprites != null && nonRecyclableSprites.Length > 0)
        {
            selectedSprite = nonRecyclableSprites[Random.Range(0, nonRecyclableSprites.Length)];
        }
    }

    float randomX = Random.Range(spawnMinX, spawnMaxX);
    Vector3 spawnPos = new Vector3(randomX, spawnY, -1f);

    GameObject item = Instantiate(itemPrefab, spawnPos, Quaternion.identity);

    var fallingItem = item.GetComponent<FallingItem>();
    if (fallingItem != null)
        fallingItem.Initialize(fallSpeed, this, selectedSprite, randomType);
}
```

#### Line 104: Random Type Selection

```csharp
ItemType randomType = (Random.value > 0.5f) ? ItemType.Recyclable : ItemType.NonRecyclable;
```

**What is `Random.value`:**

- Returns random float between 0.0 and 1.0
- 50% chance of being > 0.5

**Ternary operator:**

```
condition ? value_if_true : value_if_false
```

**Result:** 50/50 chance of recyclable vs non-recyclable.

---

#### Lines 108-121: Select Random Sprite

```csharp
if (randomType == ItemType.Recyclable)
{
    if (recyclableSprites != null && recyclableSprites.Length > 0)
    {
        selectedSprite = recyclableSprites[Random.Range(0, recyclableSprites.Length)];
    }
}
```

**What is `Random.Range(0, array.Length)`:**

- Returns random integer from 0 to Length-1
- Selects random index in the array

---

#### Lines 123-124: Calculate Spawn Position

```csharp
float randomX = Random.Range(spawnMinX, spawnMaxX);
Vector3 spawnPos = new Vector3(randomX, spawnY, -1f);
```

**Random X between -8 and 8:**

- Items spawn at random horizontal positions
- Creates variety in gameplay

**Z = -1:**

- Ensures items render in front of background
- Lower Z = closer to camera in 2D

---

#### Line 126: Instantiate Item

```csharp
GameObject item = Instantiate(itemPrefab, spawnPos, Quaternion.identity);
```

**What is Instantiate:**

- Creates a copy of the prefab
- Places it at spawnPos
- No rotation (Quaternion.identity)

---

#### Lines 128-130: Initialize FallingItem

```csharp
var fallingItem = item.GetComponent<FallingItem>();
if (fallingItem != null)
    fallingItem.Initialize(fallSpeed, this, selectedSprite, randomType);
```

**What it does:**

- Get the FallingItem component
- Initialize it with speed, manager reference, sprite, and type

---

### Lines 136-166: OnItemClicked Method

```csharp
public void OnItemClicked(ItemType clickedType)
{
    if (clickedType == ItemType.Recyclable)
    {
        // Correct Click
        correctClicks++;
        totalEarnedGP += pointsPerRecyclable;

        // Award GreenPoints immediately
        if (GreenPointsManager.Instance != null)
            GreenPointsManager.Instance.AddPoints(pointsPerRecyclable);

        AudioManager.Instance?.PlayClick();
        Debug.Log($"✓ Correct! Recyclable item clicked. +{pointsPerRecyclable} GP");
    }
    else
    {
        // Wrong Click
        wrongClicks++;
        totalEarnedGP -= penaltyPerNonRecyclable;

        // Deduct GreenPoints as penalty
        if (GreenPointsManager.Instance != null)
            GreenPointsManager.Instance.DeductPoints(penaltyPerNonRecyclable);

        AudioManager.Instance?.PlayCancel();
        Debug.Log($"✗ Wrong! Non-recyclable item clicked. -{penaltyPerNonRecyclable} GP");
    }

    UpdateScoreUI();
}
```

**Called by:** `FallingItem.Collect()` when player clicks an item

**What it does:**

1. Check item type
2. If recyclable: increment correct count, add GP
3. If non-recyclable: increment wrong count, deduct GP
4. Update UI

---

### Lines 168-175: EndGame Method

```csharp
void EndGame()
{
    gameActive = false;

    ShowGameOver();

    Debug.Log($"Game Over! Correct: {correctClicks}, Wrong: {wrongClicks}, Total GP: {totalEarnedGP}");
}
```

**What it does:**

- Stop the game loop
- Show game over UI
- Log final stats

---

### Lines 177-192: ShowGameOver Method

```csharp
void ShowGameOver()
{
    if (gameOverPanel != null)
        gameOverPanel.SetActive(true);

    if (finalScoreText != null)
        finalScoreText.text = $"Recycled Items: {correctClicks}";

    if (rewardText != null)
    {
        string sign = totalEarnedGP >= 0 ? "+" : "";
        rewardText.text = $"Green Points: {sign}{totalEarnedGP}";
    }

    Invoke(nameof(ReturnToVillage), 3f);
}
```

#### Line 187: Sign Logic

```csharp
string sign = totalEarnedGP >= 0 ? "+" : "";
```

**What it does:**

- If positive or zero: add "+" prefix
- If negative: number already has "-" sign

**Result:**

- `totalEarnedGP = 50` → "+50"
- `totalEarnedGP = -20` → "-20"

---

#### Line 191: Delayed Scene Change

```csharp
Invoke(nameof(ReturnToVillage), 3f);
```

**What is Invoke:**

- Unity function to call a method after delay
- `nameof(ReturnToVillage)` = "ReturnToVillage" (string)
- `3f` = 3 seconds delay

**Why use Invoke:**

- Gives player time to see their score
- Smooth transition rather than instant

---

### Lines 194-197: ReturnToVillage Method

```csharp
void ReturnToVillage()
{
    SceneManager.LoadScene("Village");
}
```

**What it does:**

- Loads the Village scene
- Completely unloads the current scene
- All objects in EcoCollector scene are destroyed

---

### Lines 199-206: UpdateTimerUI Method

```csharp
void UpdateTimerUI()
{
    if (timerText != null)
    {
        int seconds = Mathf.CeilToInt(timeRemaining);
        timerText.text = $"Time: {seconds}s";
    }
}
```

#### What is Mathf.CeilToInt:

- Rounds UP to nearest integer
- `CeilToInt(29.1)` → 30
- `CeilToInt(29.9)` → 30

**Why ceiling:**

- Shows "30" until it's truly below 30
- More intuitive for players

---

### Lines 208-215: UpdateScoreUI Method

```csharp
void UpdateScoreUI()
{
    if (scoreText != null)
        scoreText.text = $"Correct: {correctClicks} | Wrong: {wrongClicks}";

    if (greenPointsText != null && GreenPointsManager.Instance != null)
        greenPointsText.text = $"GreenPoints: {GreenPointsManager.Instance.GreenPoints}";
}
```

**What it does:**

- Updates the score display
- Shows current GreenPoints from the global manager

---

### Lines 217-225: Restart and Exit

```csharp
public void RestartGame()
{
    SceneManager.LoadScene(SceneManager.GetActiveScene().name);
}

public void ExitGame()
{
    ReturnToVillage();
}
```

**RestartGame:**

- Reloads the current scene
- `GetActiveScene().name` gets current scene name

**ExitGame:**

- Same as returning to village
- Could be attached to an "Exit" button

---

# How Everything Works Together

## Complete Game Flow

```
1. SCENE LOADS
   └── EcoCollectorGame.Start()
       └── StartGame()
           ├── timeRemaining = 30
           ├── Reset counters
           ├── gameActive = true
           └── Update UI

2. EVERY FRAME (while gameActive)
   └── EcoCollectorGame.Update()
       ├── Countdown timer
       ├── Update timer UI
       ├── Check if time <= 0 → EndGame()
       └── Spawn timer
           └── If interval passed → SpawnItem()
               ├── Random type (50/50)
               ├── Random sprite
               ├── Random X position
               ├── Instantiate prefab
               └── Initialize FallingItem

3. FALLING ITEM UPDATES
   └── FallingItem.Update()
       ├── Move down: position += Vector3.down * speed * deltaTime
       └── If y < -6 → Destroy

4. PLAYER CLICKS ITEM
   └── FallingItem.OnMouseDown()
       └── Collect()
           ├── collected = true
           ├── gameManager.OnItemClicked(itemType)
           │   └── EcoCollectorGame.OnItemClicked()
           │       ├── If Recyclable: +10 GP
           │       ├── If NonRecyclable: -5 GP
           │       └── Update UI
           └── Destroy(gameObject)

5. TIMER REACHES ZERO
   └── EcoCollectorGame.EndGame()
       ├── gameActive = false
       └── ShowGameOver()
           ├── Show panel
           ├── Display final scores
           └── Invoke(ReturnToVillage, 3f)

6. AFTER 3 SECONDS
   └── ReturnToVillage()
       └── SceneManager.LoadScene("Village")
```

---

# Common Mistakes & Notes

## Mistake 1: Prefab Missing Components

**Problem:** itemPrefab doesn't have FallingItem or Collider2D
**Result:** Items spawn but can't be clicked, or NullReferenceException
**Fix:** Ensure prefab has: FallingItem, SpriteRenderer, Collider2D

## Mistake 2: Wrong Collider Type

**Problem:** Using 3D Collider (BoxCollider) instead of 2D (BoxCollider2D)
**Result:** OnMouseDown never triggers
**Fix:** Use BoxCollider2D or CircleCollider2D

## Mistake 3: Empty Sprite Arrays

**Problem:** recyclableSprites or nonRecyclableSprites have size 0
**Result:** Items spawn without visible sprite
**Fix:** Assign sprites in Inspector

## Mistake 4: Scene Not in Build Settings

**Problem:** "Village" scene not added to Build Settings
**Result:** Error when trying to load scene
**Fix:** Add Village scene in File > Build Settings

## Mistake 5: Missing UI References

**Problem:** timerText, scoreText, etc. not assigned
**Result:** UI doesn't update (but no crash due to null checks)
**Fix:** Assign all UI references in Inspector

---

# Summary Table

| Script                | Responsibility                                     |
| --------------------- | -------------------------------------------------- |
| `FallingItem.cs`      | Individual item behavior, falling, click detection |
| `EcoCollectorGame.cs` | Game loop, spawning, scoring, UI, scene management |

| Communication Flow                                        |
| --------------------------------------------------------- |
| EcoCollectorGame spawns → FallingItem                     |
| FallingItem clicked → EcoCollectorGame.OnItemClicked()    |
| EcoCollectorGame → GreenPointsManager (add/deduct points) |
| EcoCollectorGame → SceneManager (load Village)            |
