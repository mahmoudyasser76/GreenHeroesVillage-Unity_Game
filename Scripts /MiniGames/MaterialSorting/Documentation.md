# 📚 MaterialSorting Mini-Game - Complete Line-by-Line Documentation

**Folder Path:** `Assets/Scripts/MiniGames/MaterialSorting`

This document provides an **extremely detailed, line-by-line explanation** of ALL scripts in the MaterialSorting mini-game folder.

---

# Table of Contents

1. [Overview](#overview)
2. [Script 1: MaterialType.cs (Enum)](#script-1-materialtypecs)
3. [Script 2: MaterialBin.cs (Recycling Bin)](#script-2-materialbincs)
4. [Script 3: MaterialSortingItem.cs (Draggable Item)](#script-3-materialsortingitemcs)
5. [Script 4: MaterialSortingGameManager.cs (Game Controller)](#script-4-materialsortinggamemanagercs)
6. [How Everything Works Together](#how-everything-works-together)

---

# Overview

The **Material Sorting** mini-game teaches recycling by material type:

- Items (plastic bottles, paper, metal cans) appear on screen
- Player **drags** items to the **correct recycling bin**
- Three bins: **Plastic**, **Paper**, **Metal**
- **+10 GP** for correct placement, **-2 GP** for wrong
- Wrong placements return to original position (retry allowed)
- Visual feedback with **stars** and **particle effects**

**Scripts in this folder:**

| Script                          | Lines | Purpose                                 |
| ------------------------------- | ----- | --------------------------------------- |
| `MaterialType.cs`               | 12    | Enum defining material categories       |
| `MaterialBin.cs`                | 37    | Recycling bin - validates dropped items |
| `MaterialSortingItem.cs`        | 147   | Draggable item with drop detection      |
| `MaterialSortingGameManager.cs` | 328   | Main game controller                    |

---

# Script 1: MaterialType.cs

**File Path:** `Assets/Scripts/MiniGames/MaterialSorting/MaterialType.cs`

## Script Purpose

Defines the **types of recyclable materials** in the game. This enum is used by both items (what type they are) and bins (what type they accept).

---

## Complete Script Code

```csharp
using UnityEngine;

/// <summary>
/// Types of materials in the sorting game
/// </summary>
public enum MaterialType
{
    Plastic,  // Plastic
    Paper,    // Paper
    Metal     // Metal
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

#### What it does:

- Imports Unity's core namespace
- **Not strictly required** for a pure enum, but good practice
- Allows potential future use of Unity types

---

## Enum Declaration

### Lines 6-11: MaterialType Enum

```csharp
public enum MaterialType
{
    Plastic,  // Plastic
    Paper,    // Paper
    Metal     // Metal
}
```

#### What is an Enum:

- A type with a fixed set of named constants
- Each value is internally an integer (Plastic=0, Paper=1, Metal=2)
- Type-safe: compiler catches typos

#### `public`:

- Accessible from any script in the project
- Both MaterialBin and MaterialSortingItem use this

#### The Three Values:

| Value     | Internal Integer | Usage                        |
| --------- | ---------------- | ---------------------------- |
| `Plastic` | 0                | Plastic bottles, containers  |
| `Paper`   | 1                | Paper, cardboard, newspapers |
| `Metal`   | 2                | Cans, metal objects          |

#### Why use an Enum here:

**Without enum (BAD):**

```csharp
string binType = "Plastik";  // Typo! No error shown
if (itemType == "plastic") {} // Case mismatch! Bug!
```

**With enum (GOOD):**

```csharp
MaterialType binType = MaterialType.Plastik;  // Compilation ERROR! Typo caught!
if (itemType == MaterialType.Plastic) {} // Works correctly
```

---

---

# Script 2: MaterialBin.cs

**File Path:** `Assets/Scripts/MiniGames/MaterialSorting/MaterialBin.cs`

## Script Purpose

`MaterialBin` represents a **recycling bin** that accepts one type of material. It:

1. Stores what material type this bin accepts
2. Validates if a dropped item is the correct type
3. Plays particle effects on correct placement

---

## Complete Script Code

```csharp
using UnityEngine;
using UnityEngine.UI;

public class MaterialBin : MonoBehaviour
{
    [Header("Bin Settings")]
    [Tooltip("Material type for this bin")]
    public MaterialType binType = MaterialType.Plastic;

    [Header("Visual")]
    public ParticleSystem successParticles;

    public bool CheckMatch(MaterialType itemType)
    {
        bool isMatch = (itemType == binType);

        if (isMatch)
        {
            Debug.Log($"MaterialBin: Match! {itemType} in {binType} bin");

            if (successParticles != null)
            {
                successParticles.Play();
            }
        }
        else
        {
            Debug.Log($"MaterialBin: No match. Item: {itemType}, Bin: {binType}");
        }

        return isMatch;
    }
}
```

---

## Imports / Using Statements

### Line 1: `using UnityEngine;`

```csharp
using UnityEngine;
```

Provides: `MonoBehaviour`, `Debug`, `ParticleSystem`, attributes

### Line 2: `using UnityEngine.UI;`

```csharp
using UnityEngine.UI;
```

Provides: UI components (not actually used in current code - could be removed)

---

## Class Declaration

### Line 7: Class Definition

```csharp
public class MaterialBin : MonoBehaviour
```

- `public class MaterialBin`: Accessible from other scripts
- `: MonoBehaviour`: Can be attached to GameObjects

---

## Variables & Fields

### Lines 9-11: Bin Settings

```csharp
[Header("Bin Settings")]
[Tooltip("Material type for this bin")]
public MaterialType binType = MaterialType.Plastic;
```

#### `binType`:

**What it stores:**

- Which material type this bin accepts
- Set per bin in the Inspector

**Default `= MaterialType.Plastic`:**

- If not changed, bin accepts plastic
- Each of 3 bins should be set to different types

**In Inspector:**

```
▼ Bin Settings
   Bin Type: Plastic ▼  (dropdown: Plastic, Paper, Metal)
```

---

### Lines 13-14: Visual Effects

```csharp
[Header("Visual")]
public ParticleSystem successParticles;
```

#### What is ParticleSystem:

- Unity component for visual effects (sparks, explosions, confetti)
- Creates many small images/sprites that animate

#### `successParticles`:

- Optional particle effect for correct placements
- Plays celebration effect when item is correctly sorted

---

## Custom Functions

### Lines 16-35: CheckMatch Method

```csharp
public bool CheckMatch(MaterialType itemType)
{
    bool isMatch = (itemType == binType);

    if (isMatch)
    {
        Debug.Log($"MaterialBin: Match! {itemType} in {binType} bin");

        if (successParticles != null)
        {
            successParticles.Play();
        }
    }
    else
    {
        Debug.Log($"MaterialBin: No match. Item: {itemType}, Bin: {binType}");
    }

    return isMatch;
}
```

#### Method Signature

```csharp
public bool CheckMatch(MaterialType itemType)
```

- `public`: Called from MaterialSortingItem
- `bool`: Returns true if match, false if not
- `MaterialType itemType`: The type of item being dropped

#### Line 18: Compare Types

```csharp
bool isMatch = (itemType == binType);
```

**What it does:**

- Compares the dropped item's type to this bin's type
- `true` if they match, `false` otherwise

**Example:**

```
Item: MaterialType.Paper
Bin: MaterialType.Paper
Result: true (Match!)

Item: MaterialType.Plastic
Bin: MaterialType.Paper
Result: false (No match)
```

#### Lines 24-27: Play Particles on Match

```csharp
if (successParticles != null)
{
    successParticles.Play();
}
```

**What `.Play()` does:**

- Starts the particle effect
- Particles spawn and animate according to particle system settings
- Creates visual celebration feedback

---

---

# Script 3: MaterialSortingItem.cs

**File Path:** `Assets/Scripts/MiniGames/MaterialSorting/MaterialSortingItem.cs`

## Script Purpose

`MaterialSortingItem` is a **draggable recycling item**. It:

1. Displays an item sprite (bottle, paper, can)
2. Handles drag-and-drop with UI drag interfaces
3. Detects when dropped on a bin
4. Returns to original position if dropped incorrectly

---

## Complete Script Code

```csharp
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;

public class MaterialSortingItem : MonoBehaviour, IBeginDragHandler, IDragHandler, IEndDragHandler
{
    private Image itemImage;
    private MaterialType itemType;
    private Vector3 originalPosition;
    private MaterialSortingGameManager gameManager;
    private Canvas canvas;
    private bool isDragging = false;
    private bool isPlaced = false;

    void Awake() { /* Get components */ }
    void Start() { /* Store original position */ }

    public void Initialize(MaterialType type, Sprite sprite, MaterialSortingGameManager manager) { /* Setup */ }

    public void OnBeginDrag(PointerEventData eventData) { /* Start drag */ }
    public void OnDrag(PointerEventData eventData) { /* Move with pointer */ }
    public void OnEndDrag(PointerEventData eventData) { /* Check for bin */ }

    private void CheckForBin(PointerEventData eventData) { /* Raycast for bins */ }
    private IEnumerator ReturnToOriginalPosition() { /* Animate return */ }
    public void ResetItem() { /* Full reset */ }
}
```

---

## Imports / Using Statements

### Lines 1-3:

```csharp
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;
```

| Import                     | Provides                                       |
| -------------------------- | ---------------------------------------------- |
| `UnityEngine`              | MonoBehaviour, Vector3, Time, Debug            |
| `UnityEngine.EventSystems` | Drag interfaces, PointerEventData, EventSystem |
| `UnityEngine.UI`           | Image component                                |

---

## Class Declaration

### Line 8: Class with Drag Interfaces

```csharp
public class MaterialSortingItem : MonoBehaviour, IBeginDragHandler, IDragHandler, IEndDragHandler
```

#### The Three Drag Interfaces:

| Interface           | Required Method | When Called             |
| ------------------- | --------------- | ----------------------- |
| `IBeginDragHandler` | `OnBeginDrag()` | When drag starts        |
| `IDragHandler`      | `OnDrag()`      | Every frame during drag |
| `IEndDragHandler`   | `OnEndDrag()`   | When drag ends          |

---

## Variables & Fields

### Lines 10-16: Private Fields

```csharp
private Image itemImage;
private MaterialType itemType;
private Vector3 originalPosition;
private MaterialSortingGameManager gameManager;
private Canvas canvas;
private bool isDragging = false;
private bool isPlaced = false;
```

| Variable           | Purpose                                      |
| ------------------ | -------------------------------------------- |
| `itemImage`        | Image component for displaying sprite        |
| `itemType`         | What type this item is (Plastic/Paper/Metal) |
| `originalPosition` | Where item started (for return animation)    |
| `gameManager`      | Reference to notify on placement             |
| `canvas`           | Parent Canvas for coordinate conversion      |
| `isDragging`       | Currently being dragged?                     |
| `isPlaced`         | Successfully placed in correct bin?          |

---

## Unity Lifecycle Methods

### Lines 18-22: Awake Method

```csharp
void Awake()
{
    itemImage = GetComponent<Image>();
    canvas = GetComponentInParent<Canvas>();
}
```

#### When Unity calls Awake():

- First lifecycle method
- Called when object is created (before Start)

#### Line 20: `itemImage = GetComponent<Image>();`

**What it does:**

- Gets the Image component on this GameObject
- Used to set the sprite later

#### Line 21: `canvas = GetComponentInParent<Canvas>();`

**What is `GetComponentInParent<T>()`:**

- Searches UP the hierarchy for a component
- Finds the Canvas this UI element belongs to
- Needed for coordinate conversion during drag

---

### Lines 24-27: Start Method

```csharp
void Start()
{
    originalPosition = transform.position;
}
```

**What it does:**

- Stores the starting position
- Used to return item if placed incorrectly

---

## Custom Functions

### Lines 29-45: Initialize Method

```csharp
public void Initialize(MaterialType type, Sprite sprite, MaterialSortingGameManager manager)
{
    itemType = type;
    gameManager = manager;

    Debug.Log($"[MaterialSortingItem] Initialize called - Type: {type}, Sprite: {sprite?.name ?? "NULL"}");

    if (itemImage != null && sprite != null)
    {
        itemImage.sprite = sprite;
    }
    else
    {
        Debug.LogError($"[MaterialSortingItem] Cannot set sprite!");
    }
}
```

#### What it does:

1. Store the material type
2. Store reference to game manager
3. Set the visual sprite

#### Line 34: Null-conditional with null-coalescing

```csharp
sprite?.name ?? "NULL"
```

**Breaking it down:**

- `sprite?.name`: If sprite is not null, get its name; otherwise null
- `?? "NULL"`: If the result is null, use "NULL" instead

---

### Lines 47-51: OnBeginDrag

```csharp
public void OnBeginDrag(PointerEventData eventData)
{
    if (isPlaced) return;
    isDragging = true;
}
```

**What it does:**

- Checks if already correctly placed (can't drag again)
- Sets dragging flag to true

---

### Lines 53-68: OnDrag

```csharp
public void OnDrag(PointerEventData eventData)
{
    if (!isDragging || isPlaced) return;

    if (canvas != null)
    {
        Vector2 position;
        RectTransformUtility.ScreenPointToLocalPointInRectangle(
            canvas.transform as RectTransform,
            eventData.position,
            canvas.worldCamera,
            out position
        );
        transform.position = canvas.transform.TransformPoint(position);
    }
}
```

#### Lines 60-65: Coordinate Conversion

```csharp
RectTransformUtility.ScreenPointToLocalPointInRectangle(
    canvas.transform as RectTransform,  // The parent rect
    eventData.position,                   // Mouse/touch screen position
    canvas.worldCamera,                   // Camera (null for overlay canvas)
    out position                          // OUTPUT: local position
);
```

**Why this is needed:**

- `eventData.position` is in **screen pixels** (0,0 = bottom-left)
- UI elements use **local canvas coordinates**
- This method converts between coordinate systems

#### Line 66: Apply Position

```csharp
transform.position = canvas.transform.TransformPoint(position);
```

- `TransformPoint()`: Converts local-to-world coordinates
- Sets the item's world position

---

### Lines 70-75: OnEndDrag

```csharp
public void OnEndDrag(PointerEventData eventData)
{
    if (!isDragging || isPlaced) return;
    isDragging = false;
    CheckForBin(eventData);
}
```

**What it does:**

1. Exit if not in valid drag state
2. Stop dragging
3. Check if dropped on a bin

---

### Lines 77-119: CheckForBin Method

```csharp
private void CheckForBin(PointerEventData eventData)
{
    var results = new System.Collections.Generic.List<RaycastResult>();
    EventSystem.current.RaycastAll(eventData, results);

    foreach (var result in results)
    {
        MaterialBin bin = result.gameObject.GetComponent<MaterialBin>();
        if (bin != null)
        {
            bool isCorrect = bin.CheckMatch(itemType);

            if (isCorrect)
            {
                isPlaced = true;
            }

            if (gameManager != null)
            {
                gameManager.OnItemPlaced(isCorrect, bin.transform.position, this.gameObject);
            }

            if (!isCorrect)
            {
                StartCoroutine(ReturnToOriginalPosition());
            }

            return;
        }
    }

    // No bin found - return to original position
    if (gameManager != null)
    {
        gameManager.OnItemPlaced(false, originalPosition, this.gameObject);
    }

    StartCoroutine(ReturnToOriginalPosition());
}
```

#### Lines 79-80: Raycast for UI Elements

```csharp
var results = new System.Collections.Generic.List<RaycastResult>();
EventSystem.current.RaycastAll(eventData, results);
```

**What is RaycastAll:**

- Casts a ray from the pointer position
- Finds ALL UI elements under that point
- Returns list of `RaycastResult` objects

**Why use EventSystem.RaycastAll:**

- Works with UI elements (Canvas-based)
- `Physics.Raycast` is for 3D world objects
- UI has its own raycast system

#### Lines 82-109: Check Each Result

```csharp
foreach (var result in results)
{
    MaterialBin bin = result.gameObject.GetComponent<MaterialBin>();
    if (bin != null)
    {
        bool isCorrect = bin.CheckMatch(itemType);
        // ...
    }
}
```

**What this does:**

1. Loop through all UI elements at drop position
2. Check if any has a MaterialBin component
3. If found, validate the match

#### Lines 89-93: Handle Correct Placement

```csharp
if (isCorrect)
{
    isPlaced = true;
}
```

- Only set `isPlaced = true` if correct
- If wrong, player can try again

#### Lines 103-106: Handle Wrong Placement

```csharp
if (!isCorrect)
{
    StartCoroutine(ReturnToOriginalPosition());
}
```

- Wrong placement → animate back to start
- Player can try again

---

### Lines 121-138: ReturnToOriginalPosition Coroutine

```csharp
private System.Collections.IEnumerator ReturnToOriginalPosition()
{
    yield return new UnityEngine.WaitForSeconds(0.3f);

    float duration = 0.3f;
    float elapsed = 0f;
    Vector3 startPos = transform.position;

    while (elapsed < duration)
    {
        elapsed += Time.deltaTime;
        transform.position = Vector3.Lerp(startPos, originalPosition, elapsed / duration);
        yield return null;
    }

    transform.position = originalPosition;
    isPlaced = false;
}
```

#### What is a Coroutine:

- A method that can pause and resume
- Uses `yield return` to pause
- Runs alongside Update() without blocking

#### Line 123: Initial Delay

```csharp
yield return new UnityEngine.WaitForSeconds(0.3f);
```

- Wait 0.3 seconds before starting animation
- Gives visual feedback time

#### Lines 129-133: Animation Loop

```csharp
while (elapsed < duration)
{
    elapsed += Time.deltaTime;
    transform.position = Vector3.Lerp(startPos, originalPosition, elapsed / duration);
    yield return null;
}
```

**How Lerp animation works:**

- Each frame, calculate progress: `elapsed / duration`
- Lerp between start and target based on progress
- `yield return null` = wait one frame

**Timeline:**

```
Frame 1: elapsed=0.016, progress=0.05, position=5% towards target
Frame 2: elapsed=0.032, progress=0.11, position=11% towards target
...
Frame 18: elapsed=0.3, progress=1.0, position=at target
```

---

---

# Script 4: MaterialSortingGameManager.cs

**File Path:** `Assets/Scripts/MiniGames/MaterialSorting/MaterialSortingGameManager.cs`

## Script Purpose

`MaterialSortingGameManager` is the **main controller** for the Material Sorting game. It manages:

1. **Item spawning** - Creating draggable items with random types
2. **Scoring** - Tracking correct/wrong placements
3. **Visual feedback** - Stars, particles, UI updates
4. **Game flow** - Rounds, completion, restart

---

## Complete Script Code (Key Parts)

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;
using TMPro;

public class MaterialSortingGameManager : MonoBehaviour
{
    [Header("Game Settings")]
    public int itemsPerRound = 5;

    [Header("Prefabs")]
    public GameObject itemPrefab;

    [Header("Material Sprites")]
    public Sprite[] plasticSprites;
    public Sprite[] paperSprites;
    public Sprite[] metalSprites;

    [Header("Spawn Settings")]
    public Transform itemSpawnPoint;

    [Header("UI")]
    public GameObject encouragementPanel;
    public Transform starsDisplay;
    public GameObject starPrefab;
    public TextMeshProUGUI greenPointsText;

    [Header("Audio")]
    public AudioClip clapSound;
    public AudioSource audioSource;

    [Header("Scoring")]
    public int pointsPerCorrect = 10;
    public int pointsPerWrong = 2;

    // Private state
    private int currentScore = 0;
    private int earnedPoints = 0;
    private List<GameObject> activeItems = new List<GameObject>();
    private MaterialType[] availableTypes = { MaterialType.Plastic, MaterialType.Paper, MaterialType.Metal };

    // ... methods
}
```

---

## Imports / Using Statements

### Lines 1-4:

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.SceneManagement;
using TMPro;
```

| Import                        | Provides                   |
| ----------------------------- | -------------------------- |
| `System.Collections`          | IEnumerator for coroutines |
| `UnityEngine`                 | Core Unity types           |
| `UnityEngine.SceneManagement` | SceneManager.LoadScene()   |
| `TMPro`                       | TextMeshProUGUI            |

---

## Variables & Fields

### Lines 12-13: Game Settings

```csharp
[Header("Game Settings")]
public int itemsPerRound = 5;
```

**`itemsPerRound = 5`:**

- How many items spawn each round
- Actually limited to 3 in code (to match 3 bins)

---

### Lines 18-26: Material Sprites

```csharp
[Header("Material Sprites")]
[Tooltip("Plastic sprites")]
public Sprite[] plasticSprites;

[Tooltip("Paper sprites")]
public Sprite[] paperSprites;

[Tooltip("Metal sprites")]
public Sprite[] metalSprites;
```

**What these are:**

- Arrays of sprites for each material type
- Random sprite selected when spawning item

**In Inspector:**

```
▼ Plastic Sprites
   Size: 3
   Element 0: PlasticBottle1 (Sprite)
   Element 1: PlasticBottle2 (Sprite)
   Element 2: PlasticContainer (Sprite)
```

---

### Lines 31-39: UI References

```csharp
[Header("UI")]
public GameObject encouragementPanel;
public Transform starsDisplay;
public GameObject starPrefab;
public TextMeshProUGUI greenPointsText;
```

| Field                | Purpose                    |
| -------------------- | -------------------------- |
| `encouragementPanel` | Shown at game end          |
| `starsDisplay`       | Parent for star icons      |
| `starPrefab`         | Star prefab to instantiate |
| `greenPointsText`    | Score display              |

---

### Lines 47-53: Private State

```csharp
private int currentScore = 0;
private int earnedPoints = 0;
private System.Collections.Generic.List<GameObject> activeItems = new System.Collections.Generic.List<GameObject>();
private MaterialType[] availableTypes = new MaterialType[]
{
    MaterialType.Plastic,
    MaterialType.Paper,
    MaterialType.Metal
};
```

| Variable         | Purpose                          |
| ---------------- | -------------------------------- |
| `currentScore`   | Number of correctly sorted items |
| `earnedPoints`   | Total GreenPoints earned         |
| `activeItems`    | Currently spawned items          |
| `availableTypes` | Array of all material types      |

---

## Unity Lifecycle Methods

### Lines 55-78: Start Method

```csharp
void Start()
{
    if (encouragementPanel != null)
        encouragementPanel.SetActive(false);

    if (audioSource == null)
        audioSource = gameObject.AddComponent<AudioSource>();

    // CRITICAL: Ensure EventSystem exists
    if (UnityEngine.EventSystems.EventSystem.current == null)
    {
        Debug.LogError("No EventSystem found! Drag and drop won't work!");
        GameObject eventSystem = new GameObject("EventSystem");
        eventSystem.AddComponent<UnityEngine.EventSystems.EventSystem>();
        eventSystem.AddComponent<UnityEngine.EventSystems.StandaloneInputModule>();
    }

    SpawnAllItems();
}
```

#### Lines 64-71: Ensure EventSystem Exists

```csharp
if (UnityEngine.EventSystems.EventSystem.current == null)
{
    Debug.LogError("No EventSystem found!");
    GameObject eventSystem = new GameObject("EventSystem");
    eventSystem.AddComponent<UnityEngine.EventSystems.EventSystem>();
    eventSystem.AddComponent<UnityEngine.EventSystems.StandaloneInputModule>();
}
```

**Why this is CRITICAL:**

- Drag/drop requires EventSystem to work
- Without it, no UI events are processed
- This auto-creates one as fallback

**What EventSystem needs:**

- `EventSystem` component - processes input
- `StandaloneInputModule` - handles mouse/keyboard

---

## Custom Functions

### Lines 80-177: SpawnAllItems Method

```csharp
public void SpawnAllItems()
{
    // Clear old items
    foreach (var item in activeItems)
    {
        if (item != null)
            Destroy(item);
    }
    activeItems.Clear();

    if (itemPrefab == null || itemSpawnPoint == null)
    {
        Debug.LogError("Missing prefab or spawn point!");
        return;
    }

    Vector3 basePosition = itemSpawnPoint.position;
    float[] binPositions = { -400f, 0f, 400f };
    int itemsToSpawn = Mathf.Min(itemsPerRound, 3);

    for (int i = 0; i < itemsToSpawn; i++)
    {
        float xPosition = binPositions[i];
        Vector3 spawnPos = new Vector3(xPosition, basePosition.y, 0);

        MaterialType randomType = availableTypes[Random.Range(0, availableTypes.Length)];
        Sprite selectedSprite = GetSpriteForType(randomType);

        GameObject item = Instantiate(itemPrefab, spawnPos, Quaternion.identity);

        // Parent to Canvas
        Canvas canvas = FindObjectOfType<Canvas>();
        if (canvas != null)
        {
            item.transform.SetParent(canvas.transform, false);
        }

        // Setup RectTransform
        RectTransform rectTransform = item.GetComponent<RectTransform>();
        if (rectTransform != null)
        {
            rectTransform.sizeDelta = new Vector2(150, 150);
            rectTransform.anchoredPosition = new Vector2(xPosition, 300);
        }

        // Ensure CanvasGroup for raycasting
        CanvasGroup canvasGroup = item.GetComponent<CanvasGroup>();
        if (canvasGroup == null)
        {
            canvasGroup = item.AddComponent<CanvasGroup>();
        }
        canvasGroup.interactable = true;
        canvasGroup.blocksRaycasts = true;

        // Enable raycast on Image
        UnityEngine.UI.Image img = item.GetComponent<UnityEngine.UI.Image>();
        if (img != null)
        {
            img.raycastTarget = true;
        }

        // Initialize
        MaterialSortingItem itemScript = item.GetComponent<MaterialSortingItem>();
        if (itemScript != null)
        {
            itemScript.Initialize(randomType, selectedSprite, this);
        }

        activeItems.Add(item);
    }

    UpdateUI();
}
```

#### Key Concepts Explained:

#### Line 103: Bin Positions

```csharp
float[] binPositions = { -400f, 0f, 400f };
```

**What this defines:**

- X positions for items (above each bin)
- Left bin: -400, Center: 0, Right: 400
- Items spawn directly above their potential target

#### Lines 121-126: Parent to Canvas

```csharp
Canvas canvas = FindObjectOfType<Canvas>();
if (canvas != null)
{
    item.transform.SetParent(canvas.transform, false);
}
```

**Why parent to Canvas:**

- UI elements MUST be children of a Canvas
- Otherwise they won't render
- `false` parameter = don't keep world position (use local)

#### Lines 145-153: CanvasGroup Setup

```csharp
CanvasGroup canvasGroup = item.GetComponent<CanvasGroup>();
if (canvasGroup == null)
{
    canvasGroup = item.AddComponent<CanvasGroup>();
}
canvasGroup.interactable = true;
canvasGroup.blocksRaycasts = true;
```

**What is CanvasGroup:**

- Controls interactivity of UI elements
- `interactable`: Can be clicked/dragged
- `blocksRaycasts`: Receives raycast hits

**Why `blocksRaycasts = true` is CRITICAL:**

- Without this, drag events won't register
- Item would be invisible to UI raycasts

---

### Lines 179-202: GetSpriteForType Method

```csharp
private Sprite GetSpriteForType(MaterialType type)
{
    Sprite[] sprites = null;

    switch (type)
    {
        case MaterialType.Plastic:
            sprites = plasticSprites;
            break;
        case MaterialType.Paper:
            sprites = paperSprites;
            break;
        case MaterialType.Metal:
            sprites = metalSprites;
            break;
    }

    if (sprites != null && sprites.Length > 0)
    {
        return sprites[Random.Range(0, sprites.Length)];
    }

    return null;
}
```

**What is a Switch Statement:**

- Selects code block based on value
- More readable than multiple if-else

---

### Lines 204-256: OnItemPlaced Method

```csharp
public void OnItemPlaced(bool isCorrect, Vector3 targetPosition, GameObject item)
{
    if (isCorrect)
    {
        currentScore++;
        earnedPoints += pointsPerCorrect;

        if (GreenPointsManager.Instance != null)
            GreenPointsManager.Instance.AddPoints(pointsPerCorrect);

        if (clapSound != null && audioSource != null)
            audioSource.PlayOneShot(clapSound);

        AddStar();

        // Remove item from tracking
        if (item != null && activeItems.Contains(item))
        {
            activeItems.Remove(item);
            Destroy(item, 0.3f);
        }

        // Check if round complete
        if (activeItems.Count == 0)
        {
            if (currentScore >= 9)
            {
                Invoke("ShowCompletionScreen", 1f);
            }
            else
            {
                Invoke("SpawnAllItems", 1f);
            }
        }
    }
    else
    {
        earnedPoints -= pointsPerWrong;

        if (GreenPointsManager.Instance != null)
            GreenPointsManager.Instance.DeductPoints(pointsPerWrong);
    }

    UpdateUI();
}
```

#### Game Flow Logic:

```
Item correctly placed:
├── Add score and GP
├── Play sound + add star
├── Remove item from list
└── If all items sorted:
    ├── If 9 total correct → Show completion
    └── Otherwise → Spawn new round

Item incorrectly placed:
├── Deduct GP
└── Item returns to original position (handled by item script)
```

#### Line 223: Destroy with Delay

```csharp
Destroy(item, 0.3f);
```

**What the second parameter does:**

- Delay in seconds before destruction
- Gives visual feedback time
- 0.3 seconds = 300 milliseconds

---

### Lines 260-280: AddStar and Animation

```csharp
private void AddStar()
{
    if (starsDisplay == null || starPrefab == null) return;

    GameObject star = Instantiate(starPrefab, starsDisplay);
    star.transform.localScale = Vector3.zero;
    StartCoroutine(AnimateStarAppearance(star.transform));
}

private IEnumerator AnimateStarAppearance(Transform star)
{
    float duration = 0.3f;
    float elapsed = 0f;

    while (elapsed < duration)
    {
        elapsed += Time.deltaTime;
        star.localScale = Vector3.Lerp(Vector3.zero, Vector3.one, elapsed / duration);
        yield return null;
    }
}
```

**What this creates:**

- Star starts at scale 0 (invisible)
- Animates to scale 1 over 0.3 seconds
- Creates a "pop in" visual effect

---

# How Everything Works Together

## Complete Game Flow

```
1. SCENE LOADS
   └── MaterialSortingGameManager.Start()
       ├── Hide completion panel
       ├── Ensure AudioSource exists
       ├── Ensure EventSystem exists (CRITICAL!)
       └── SpawnAllItems()
           ├── Clear old items
           ├── For each of 3 items:
           │   ├── Random material type
           │   ├── Random sprite for that type
           │   ├── Instantiate at position above bin
           │   ├── Parent to Canvas
           │   ├── Setup CanvasGroup (blocksRaycasts = true)
           │   └── Initialize MaterialSortingItem
           └── Update UI

2. PLAYER DRAGS ITEM
   ├── MaterialSortingItem.OnBeginDrag()
   │   └── isDragging = true
   ├── MaterialSortingItem.OnDrag() (every frame)
   │   └── Convert screen position → move item
   └── MaterialSortingItem.OnEndDrag()
       └── CheckForBin(eventData)

3. ITEM DROPPED
   └── CheckForBin()
       ├── RaycastAll to find UI elements under pointer
       ├── Look for MaterialBin component
       └── If bin found:
           ├── bin.CheckMatch(itemType) → true/false
           ├── gameManager.OnItemPlaced(result, position, item)
           └── If wrong: StartCoroutine(ReturnToOriginalPosition)

4. ON CORRECT PLACEMENT
   └── MaterialSortingGameManager.OnItemPlaced(true, ...)
       ├── currentScore++, earnedPoints += 10
       ├── GreenPointsManager.AddPoints(10)
       ├── PlayOneShot(clapSound)
       ├── AddStar() → animate star appearance
       ├── Remove item from activeItems
       ├── Destroy(item, 0.3f)
       └── If activeItems.Count == 0:
           ├── If score >= 9: ShowCompletionScreen
           └── Else: Invoke(SpawnAllItems, 1f)

5. ON WRONG PLACEMENT
   └── MaterialSortingGameManager.OnItemPlaced(false, ...)
       ├── earnedPoints -= 2
       ├── GreenPointsManager.DeductPoints(2)
       └── Item animates back to original position
           └── ReturnToOriginalPosition coroutine

6. GAME COMPLETE (9 items sorted)
   └── ShowCompletionScreen()
       ├── Show encouragementPanel
       └── Display final score
```

---

# Common Mistakes & Notes

## Mistake 1: No EventSystem

**Problem:** Drag events don't work
**Symptom:** Items can't be dragged at all
**Fix:** Add GameObject > UI > EventSystem (code auto-creates as fallback)

## Mistake 2: CanvasGroup.blocksRaycasts = false

**Problem:** Items can't receive drag events
**Symptom:** Clicking items does nothing
**Fix:** Ensure blocksRaycasts = true (code sets this)

## Mistake 3: Image.raycastTarget = false

**Problem:** UI raycasts pass through the item
**Symptom:** Clicks go through item to what's behind
**Fix:** Ensure raycastTarget = true on Image

## Mistake 4: Item Not Parented to Canvas

**Problem:** Item instantiated but invisible
**Symptom:** Items spawn but don't appear
**Fix:** SetParent to Canvas immediately after Instantiate

## Mistake 5: Missing Sprites

**Problem:** Items appear as white squares
**Symptom:** No visual, just blank rectangles
**Fix:** Assign sprites to plasticSprites/paperSprites/metalSprites arrays

---

# Summary Table

| Script                          | Responsibility                      |
| ------------------------------- | ----------------------------------- |
| `MaterialType.cs`               | Enum defining Plastic, Paper, Metal |
| `MaterialBin.cs`                | Validates drops, plays particles    |
| `MaterialSortingItem.cs`        | Drag/drop behavior, bin detection   |
| `MaterialSortingGameManager.cs` | Spawning, scoring, game flow        |

| Communication Flow                                |
| ------------------------------------------------- |
| GameManager spawns items with random MaterialType |
| Items detect drag via IDragHandler interfaces     |
| On drop, Item raycasts for MaterialBin            |
| Bin validates match via CheckMatch()              |
| Item notifies GameManager via OnItemPlaced()      |
| GameManager updates score, GP, stars              |
| Wrong placements → Item animates back             |
| All items complete → New round or game end        |
