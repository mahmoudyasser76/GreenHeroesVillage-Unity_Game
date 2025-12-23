# 📚 Village Scripts - Complete Line-by-Line Documentation

This document provides **extremely detailed, line-by-line explanations** of all scripts in the `Assets/Scripts/Village` folder.

---

# Table of Contents

1. [BuildingData.cs](#-script-1-buildingdatacs) - ScriptableObject for building configuration
2. [BuildingItem.cs](#-script-2-buildingitemcs) - Component on placed buildings
3. [InventoryButton.cs](#-script-3-inventorybuttoncs) - UI button for building selection
4. [Draggable.cs](#-script-4-draggablecs) - Drag-and-drop placement system
5. [DeleteManager.cs](#-script-5-deletemanagercs) - Building deletion with refund
6. [VillageManager.cs](#-script-6-villagemanagercs) - Main village controller

---

# 📗 Script 1: BuildingData.cs

**File Path:** `Assets/Scripts/Village/BuildingData.cs`

## Script Purpose

A **ScriptableObject** that defines configuration data for a building type:

- Display name and icon
- Prefab to spawn
- Cost in GreenPoints
- Size for placement

---

## Complete Script Code

```csharp
using UnityEngine;

[CreateAssetMenu(fileName = "BD_NewBuilding", menuName = "GreenHeroes/BuildingData")]
public class BuildingData : ScriptableObject
{
    [Header("Display")]
    public string displayName;
    public Sprite icon;
    public GameObject prefab;
    public Vector2 size = Vector2.one;

    [Header("Cost")]
    [Tooltip("All buildings cost 100 GreenPoints")]
    public int greenPointsCost = 100;

    public string GetCostText()
    {
        return $"{greenPointsCost}";
    }

    public int GetRefundAmount()
    {
        return greenPointsCost / 2;
    }
}
```

---

## Line-by-Line Explanation

### Line 1: `using UnityEngine;`

Imports Unity's core namespace for `ScriptableObject`, `Sprite`, `GameObject`, `Vector2`.

### Lines 3-4: Class Declaration

```csharp
[CreateAssetMenu(fileName = "BD_NewBuilding", menuName = "GreenHeroes/BuildingData")]
public class BuildingData : ScriptableObject
```

**`[CreateAssetMenu]`**: Adds "Create > GreenHeroes > BuildingData" to right-click menu in Project window.

**`: ScriptableObject`**: Inherits from ScriptableObject - stores data as asset file, not attached to GameObjects.

### Lines 6-10: Display Properties

```csharp
[Header("Display")]
public string displayName;      // Name shown in UI (e.g., "Solar House")
public Sprite icon;             // Icon for inventory button
public GameObject prefab;       // Prefab to instantiate when building
public Vector2 size = Vector2.one;  // Size for layout purposes
```

### Lines 12-14: Cost

```csharp
[Header("Cost")]
[Tooltip("All buildings cost 100 GreenPoints")]
public int greenPointsCost = 100;
```

- Default cost is 100 GP
- Can be changed per building in Inspector

### Lines 16-19: GetCostText Method

```csharp
public string GetCostText()
{
    return $"{greenPointsCost}";
}
```

Returns cost as string for UI display (e.g., "100").

### Lines 21-24: GetRefundAmount Method

```csharp
public int GetRefundAmount()
{
    return greenPointsCost / 2;
}
```

Returns 50% of cost as refund when deleted (e.g., 100 / 2 = 50).

---

# 📗 Script 2: BuildingItem.cs

**File Path:** `Assets/Scripts/Village/BuildingItem.cs`

## Script Purpose

A **MonoBehaviour component** attached to placed buildings:

- Stores reference to BuildingData
- Tracks original cost for accurate refund
- Provides refund calculation

---

## Complete Script Code

```csharp
using UnityEngine;

public class BuildingItem : MonoBehaviour
{
    [HideInInspector]
    public BuildingData data;

    [HideInInspector]
    public int originalCost = 100;

    public void Init(BuildingData bd)
    {
        data = bd;
        originalCost = bd != null ? bd.greenPointsCost : 100;
        gameObject.name = bd.displayName + "(Placed)";
    }

    public int GetRefundAmount()
    {
        return originalCost / 2;
    }
}
```

---

## Line-by-Line Explanation

### Lines 5-9: Hidden Fields

```csharp
[HideInInspector]
public BuildingData data;

[HideInInspector]
public int originalCost = 100;
```

**`[HideInInspector]`**: Field is public (accessible from code) but NOT shown in Inspector.

**Why hide these:**

- Set programmatically, not by designers
- Prevents accidental edits

### Lines 11-16: Init Method

```csharp
public void Init(BuildingData bd)
{
    data = bd;
    originalCost = bd != null ? bd.greenPointsCost : 100;
    gameObject.name = bd.displayName + "(Placed)";
}
```

**When called:** By VillageManager after spawning a building.

**What it does:**

1. Stores BuildingData reference
2. Saves original cost (for refund even if BuildingData changes later)
3. Renames GameObject to "SolarHouse(Placed)" for clarity

**Line 14: Ternary operator**

```csharp
originalCost = bd != null ? bd.greenPointsCost : 100;
```

- If bd is not null, use its cost
- Otherwise, default to 100

### Lines 18-21: GetRefundAmount Method

```csharp
public int GetRefundAmount()
{
    return originalCost / 2;
}
```

Returns 50% of the original cost (integer division).

---

# 📗 Script 3: InventoryButton.cs

**File Path:** `Assets/Scripts/Village/InventoryButton.cs`

## Script Purpose

A **UI button** in the building inventory:

- Displays building icon and name
- Handles click to spawn building

---

## Complete Script Code

```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class InventoryButton : MonoBehaviour
{
    public Image iconImage;
    public TMP_Text label;

    [HideInInspector] public BuildingData data;
    [HideInInspector] public VillageManager manager;

    public void Init(BuildingData bd, VillageManager vm)
    {
        data = bd;
        manager = vm;

        iconImage.sprite = bd.icon;
        label.text = bd.displayName;
    }

    public void OnClickPlace()
    {
        AudioManager.Instance?.PlayClick();
        manager.SpawnBuildingFromData(data, true);
    }
}
```

---

## Line-by-Line Explanation

### Lines 7-8: UI References

```csharp
public Image iconImage;    // UI Image to show building icon
public TMP_Text label;     // Text showing building name
```

- Assigned in prefab Inspector
- `TMP_Text` is TextMeshPro text component

### Lines 10-11: Hidden References

```csharp
[HideInInspector] public BuildingData data;
[HideInInspector] public VillageManager manager;
```

- Set programmatically via Init()
- Hidden to prevent manual assignment

### Lines 13-20: Init Method

```csharp
public void Init(BuildingData bd, VillageManager vm)
{
    data = bd;
    manager = vm;

    iconImage.sprite = bd.icon;
    label.text = bd.displayName;
}
```

**When called:** By VillageManager.PopulateInventory().

**What it does:**

1. Stores references
2. Sets icon sprite
3. Sets label text

### Lines 22-26: OnClickPlace Method

```csharp
public void OnClickPlace()
{
    AudioManager.Instance?.PlayClick();
    manager.SpawnBuildingFromData(data, true);
}
```

**When called:** When player clicks this inventory button.

**What it does:**

1. Plays click sound
2. Tells VillageManager to spawn this building at screen center

**`true` parameter:** Spawn at center (not at mouse position).

---

# 📗 Script 4: Draggable.cs

**File Path:** `Assets/Scripts/Village/Draggable.cs`

## Script Purpose

Handles **drag-and-drop building placement**:

- Implements Unity's pointer event interfaces
- Snaps to grid during drag
- Two modes: Placement (dragging) and Placed (clickable)

---

## Complete Script Code

```csharp
using System;
using System.Reflection;
using UnityEngine;
using UnityEngine.EventSystems;

public class Draggable : MonoBehaviour, IPointerDownHandler, IDragHandler, IPointerUpHandler
{
    public enum Mode { Placement, Placed }
    public Mode mode = Mode.Placement;

    float gridSize = 0.5f;
    Action<GameObject> onConfirm;
    Action<GameObject> onCancel;

    [HideInInspector] public VillageManager villageManager;

    bool isDragging = false;
    int originalSortingOrder = int.MinValue;
    string originalSortingLayer = null;

    static T SafeFind<T>() where T : UnityEngine.Object
    {
        // Reflection-based find for Unity version compatibility
        // Tries: FindFirstObjectByType, FindAnyObjectByType, FindObjectOfType
        // ...
    }

    public void Init(float grid, Action<GameObject> confirmCallback, Action<GameObject> cancelCallback, VillageManager vm = null)
    {
        gridSize = Mathf.Max(0.01f, grid);
        onConfirm = confirmCallback;
        onCancel = cancelCallback;

        if (vm != null)
            villageManager = vm;
        else
            villageManager = SafeFind<VillageManager>();

        // Add BoxCollider2D if missing (required for pointer events)
        if (GetComponent<Collider2D>() == null)
        {
            var sr = GetComponentInChildren<SpriteRenderer>();
            var bc = gameObject.AddComponent<BoxCollider2D>();
            bc.isTrigger = false;
            if (sr != null)
                try { bc.size = sr.bounds.size; } catch { }
        }
    }

    public void OnPointerDown(PointerEventData eventData)
    {
        if (mode == Mode.Placed)
        {
            // Clicking a placed building - select for deletion
            villageManager?.OnPlacedObjectClicked(gameObject);
            return;
        }

        // Start dragging
        isDragging = true;
        BringToFront();
    }

    public void OnDrag(PointerEventData eventData)
    {
        if (!isDragging || mode != Mode.Placement) return;

        Vector3 world = Camera.main.ScreenToWorldPoint(eventData.position);
        world.z = 0f;

        // Snap to grid
        float snappedX = Mathf.Round(world.x / gridSize) * gridSize;
        float snappedY = Mathf.Round(world.y / gridSize) * gridSize;
        transform.position = new Vector3(snappedX, snappedY, transform.position.z);
    }

    public void OnPointerUp(PointerEventData eventData)
    {
        if (mode != Mode.Placement) return;

        isDragging = false;
        onConfirm?.Invoke(gameObject);
        RestoreSortingOrder();
    }

    void BringToFront()
    {
        var sr = GetComponent<SpriteRenderer>();
        if (sr != null)
        {
            originalSortingOrder = sr.sortingOrder;
            originalSortingLayer = sr.sortingLayerName;
            sr.sortingOrder = 1000;  // Render on top while dragging
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

    public void SetPlaced()
    {
        mode = Mode.Placed;
        isDragging = false;
    }

    public void CancelPlacement()
    {
        onCancel?.Invoke(gameObject);
    }
}
```

---

## Key Concepts

### Interface Implementation

```csharp
public class Draggable : MonoBehaviour, IPointerDownHandler, IDragHandler, IPointerUpHandler
```

**What are interfaces:**

- Contracts that define methods a class must implement
- Unity's EventSystem uses these for input handling

**The three interfaces:**

| Interface             | Required Method   | When Called                     |
| --------------------- | ----------------- | ------------------------------- |
| `IPointerDownHandler` | `OnPointerDown()` | Mouse/touch down on this object |
| `IDragHandler`        | `OnDrag()`        | Mouse/touch moves while held    |
| `IPointerUpHandler`   | `OnPointerUp()`   | Mouse/touch released            |

**Requirements for these to work:**

1. Object must have a Collider2D
2. Scene must have an EventSystem
3. Camera must have Physics2DRaycaster

### Mode Enum

```csharp
public enum Mode { Placement, Placed }
public Mode mode = Mode.Placement;
```

**Two states:**

- `Placement`: Being placed (draggable)
- `Placed`: Already placed (click to delete)

### Grid Snapping

```csharp
float snappedX = Mathf.Round(world.x / gridSize) * gridSize;
float snappedY = Mathf.Round(world.y / gridSize) * gridSize;
```

**How it works:**

1. Divide position by grid size (e.g., 2.7 / 0.5 = 5.4)
2. Round to nearest integer (5.4 → 5)
3. Multiply by grid size (5 \* 0.5 = 2.5)

**Result:** Position snaps to 0.5 increments.

---

# 📗 Script 5: DeleteManager.cs

**File Path:** `Assets/Scripts/Village/DeleteManager.cs`

## Script Purpose

Manages **building deletion**:

- Shows confirmation popup when building clicked
- Calculates and gives 50% refund
- Saves after deletion

---

## Complete Script Code

```csharp
using System;
using System.Linq;
using System.Reflection;
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class DeleteManager : MonoBehaviour
{
    [Header("UI")]
    public GameObject panelDeleteConfirm;

    [Header("Text")]
    public TextMeshProUGUI labelName;
    public TextMeshProUGUI labelRefund;

    [Header("Buttons")]
    public Button btnDelete;
    public Button btnCancel;

    private GameObject selected;
    private string lastSelectedName;

    void Awake()
    {
        // Auto-find UI elements if not assigned
        if (panelDeleteConfirm == null)
            panelDeleteConfirm = GameObject.Find("Panel_DeleteConfirm");

        // Find child elements...
    }

    void Start()
    {
        if (panelDeleteConfirm != null)
            panelDeleteConfirm.SetActive(false);

        if (btnDelete != null)
        {
            btnDelete.onClick.RemoveAllListeners();
            btnDelete.onClick.AddListener(DeleteSelected);
        }

        if (btnCancel != null)
        {
            btnCancel.onClick.RemoveAllListeners();
            btnCancel.onClick.AddListener(ClearSelection);
        }
    }

    public void Select(GameObject go)
    {
        if (go == null)
        {
            ClearSelection();
            return;
        }

        selected = go;
        lastSelectedName = go.name;

        if (panelDeleteConfirm != null)
            panelDeleteConfirm.SetActive(true);

        if (labelName != null)
            labelName.text = $"Delete: {go.name}?";

        if (labelRefund != null)
        {
            var bi = go.GetComponent<BuildingItem>();
            int refund = (bi != null) ? bi.GetRefundAmount() : 50;
            labelRefund.text = $"Refund: +{refund} GP";
        }
    }

    public void ClearSelection()
    {
        selected = null;
        lastSelectedName = null;
        if (panelDeleteConfirm != null)
            panelDeleteConfirm.SetActive(false);
    }

    public void DeleteSelected()
    {
        // Try to recover by name if reference lost
        if (selected == null && !string.IsNullOrEmpty(lastSelectedName))
        {
            // Find by name in placeRoot...
        }

        if (selected == null)
        {
            ClearSelection();
            return;
        }

        // Calculate refund
        int refundAmount = 50;
        var bi = selected.GetComponent<BuildingItem>();
        if (bi != null)
            refundAmount = bi.GetRefundAmount();

        // Give refund
        GreenPointsManager.Instance?.AddPoints(refundAmount);

        // Show message
        var villageManager = FindFirstObjectByType<VillageManager>();
        villageManager?.ShowMessage($"Deleted {selected.name}\n+{refundAmount} GreenPoints!", Color.cyan);

        // Destroy building
        Destroy(selected);
        selected = null;
        lastSelectedName = null;

        panelDeleteConfirm?.SetActive(false);

        // Save
        var sm = FindFirstObjectByType<SaveManager>();
        sm?.SaveVillage();
    }
}
```

---

## Key Flow

### Deletion Flow

```
1. Player clicks placed building
   ↓
2. Draggable.OnPointerDown() (mode = Placed)
   ↓
3. villageManager.OnPlacedObjectClicked(gameObject)
   ↓
4. deleteManager.Select(go)
   ↓
5. Show confirmation panel with refund amount
   ↓
6. Player clicks "Delete" button
   ↓
7. DeleteSelected()
   ↓
8. Calculate refund (originalCost / 2)
   ↓
9. GreenPointsManager.AddPoints(refund)
   ↓
10. Destroy(building)
    ↓
11. SaveVillage()
```

---

# 📗 Script 6: VillageManager.cs

**File Path:** `Assets/Scripts/Village/VillageManager.cs`

## Script Purpose

The **main controller** for the Village scene:

- Populates building inventory
- Handles building spawning
- Manages placement confirmation
- Shows messages
- Coordinates with DeleteManager and SaveManager

---

## Complete Script Code

```csharp
using System.Collections;
using System.Reflection;
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class VillageManager : MonoBehaviour
{
    [Header("References")]
    public Transform placeRoot;              // Parent for placed buildings
    public Transform inventoryContent;       // UI container for inventory buttons
    public GameObject inventoryItemPrefab;   // Prefab for inventory buttons

    [Header("Data")]
    public List<BuildingData> allBuildingData = new List<BuildingData>();
    public List<GameObject> buildingPrefabs = new List<GameObject>();

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

    void Start()
    {
        // Hide panels initially
        panelConfirmPlacement?.SetActive(false);
        messagePanel?.SetActive(false);

        // Setup button listeners
        confirmButton?.onClick.AddListener(ConfirmCurrentPlacement);
        cancelButton?.onClick.AddListener(CancelCurrentPlacement);

        // Populate inventory
        PopulateInventory();
    }

    public void PopulateInventory()
    {
        // Clear existing
        for (int i = inventoryContent.childCount - 1; i >= 0; i--)
            Destroy(inventoryContent.GetChild(i).gameObject);

        // Create button for each building
        foreach (var data in allBuildingData)
        {
            var go = Instantiate(inventoryItemPrefab, inventoryContent);
            go.name = $"InventoryItem_{data.displayName}";

            var ib = go.GetComponent<InventoryButton>();
            ib?.Init(data, this);

            var btn = go.GetComponent<Button>();
            btn?.onClick.AddListener(ib.OnClickPlace);
        }
    }

    public void SpawnBuildingFromData(BuildingData data, bool spawnAtCenter = false)
    {
        // Check affordability
        if (!GreenPointsManager.Instance.CanAfford(data.greenPointsCost))
        {
            ShowMessage($"Not enough GreenPoints!\nNeed {data.greenPointsCost} GP", Color.red);
            audioManager?.PlayCancel();
            return;
        }

        // Find prefab
        GameObject prefab = buildingPrefabs[allBuildingData.IndexOf(data)];

        // Calculate spawn position
        Vector3 worldPos = spawnAtCenter
            ? GetScreenCenterWorldPosition(0f)
            : Camera.main.ScreenToWorldPoint(Input.mousePosition);
        worldPos.z = 0f;

        // Spawn building
        GameObject obj = Instantiate(prefab, placeRoot);
        obj.transform.position = worldPos;
        obj.transform.localScale = defaultSpawnScale;
        obj.name = data.displayName;

        // Initialize components
        var bi = obj.GetComponent<BuildingItem>();
        bi?.Init(data);

        var dr = obj.GetComponent<Draggable>() ?? obj.AddComponent<Draggable>();
        dr.Init(gridSize, OnPlacementConfirmed, OnPlacementCanceled, this);

        currentObject = obj;
        currentDraggable = dr;

        ShowConfirmPanel();
        audioManager?.PlayClick();
    }

    public void OnPlacementConfirmed(GameObject placed)
    {
        if (placed == null) placed = currentObject;
        if (placed == null) return;

        // Spend GreenPoints
        var bi = placed.GetComponent<BuildingItem>();
        if (bi?.data != null)
        {
            int cost = bi.data.greenPointsCost;
            if (!GreenPointsManager.Instance.TrySpend(cost))
            {
                ShowMessage($"Not enough GreenPoints!", Color.red);
                Destroy(placed);
                HideConfirmPanel();
                return;
            }

            ShowMessage($"Built {bi.data.displayName}!\n-{cost} GreenPoints", Color.green);
        }

        // Mark as placed
        placed.GetComponent<Draggable>()?.SetPlaced();

        HideConfirmPanel();
        currentObject = null;

        saveManager?.SaveVillage();
        audioManager?.PlayPlace();
    }

    public void OnPlacementCanceled(GameObject canceled)
    {
        if (canceled == null) canceled = currentObject;
        if (canceled != null) Destroy(canceled);

        HideConfirmPanel();
        currentObject = null;

        audioManager?.PlayCancel();
    }

    public void OnPlacedObjectClicked(GameObject obj)
    {
        // Forward to DeleteManager
        deleteManager?.Select(obj);
    }

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

    private IEnumerator HideMessageAfterDelay()
    {
        yield return new WaitForSeconds(messageDuration);
        messagePanel?.SetActive(false);
    }
}
```

---

## Key Concepts

### Coroutines

```csharp
private IEnumerator HideMessageAfterDelay()
{
    yield return new WaitForSeconds(messageDuration);
    messagePanel?.SetActive(false);
}
```

**What is a Coroutine:**

- A method that can pause execution and resume later
- Uses `yield return` to pause
- Started with `StartCoroutine()`

**`yield return new WaitForSeconds(2f)`:**

- Pauses for 2 seconds
- Then continues to next line

**Why use Coroutines:**

- Non-blocking delays (game continues running)
- Alternative to tracking timers in Update()

### Null-Conditional Operator

```csharp
audioManager?.PlayClick();
```

**What `?.` does:**

- If `audioManager` is null, do nothing
- If not null, call `PlayClick()`
- Prevents NullReferenceException

**Equivalent code:**

```csharp
if (audioManager != null)
    audioManager.PlayClick();
```

---

## How Everything Works Together

### Building Placement Flow

```
1. Player clicks inventory button
   ↓
2. InventoryButton.OnClickPlace()
   ↓
3. VillageManager.SpawnBuildingFromData(data, true)
   ↓
4. Check: CanAfford(100 GP)?
   ↓
   NO → ShowMessage("Not enough GP!") → STOP
   YES ↓
   ↓
5. Instantiate prefab at screen center
   ↓
6. Initialize BuildingItem and Draggable
   ↓
7. Show confirm/cancel panel
   ↓
8. Player drags building (Draggable.OnDrag)
   ↓
9. Position snaps to grid
   ↓
10. Player clicks Confirm OR releases drag
    ↓
11. OnPlacementConfirmed()
    ↓
12. TrySpend(100 GP)
    ↓
13. Draggable.SetPlaced() (mode = Placed)
    ↓
14. SaveVillage()
    ↓
15. Building is now permanent!
```

### Building Deletion Flow

```
1. Player clicks placed building
   ↓
2. Draggable.OnPointerDown() (mode = Placed)
   ↓
3. villageManager.OnPlacedObjectClicked()
   ↓
4. deleteManager.Select()
   ↓
5. Show delete confirmation panel
   ↓
6. Player clicks Delete
   ↓
7. DeleteSelected()
   ↓
8. Calculate refund: originalCost / 2
   ↓
9. AddPoints(refund)
   ↓
10. Destroy(building)
    ↓
11. SaveVillage()
```

---

## Common Mistakes & Notes

### Mistake 1: Missing Collider2D

**Problem:** Draggable events don't fire
**Fix:** Add BoxCollider2D to building prefab (or Draggable.Init adds one)

### Mistake 2: placeRoot not assigned

**Problem:** Buildings spawned but not saved
**Fix:** Create empty GameObject "PlacedBuildingsRoot", assign to placeRoot

### Mistake 3: buildingPrefabs order doesn't match allBuildingData

**Problem:** Wrong prefab spawns
**Fix:** Keep lists in same order, or use name matching

### Mistake 4: No EventSystem in scene

**Problem:** No pointer events work
**Fix:** GameObject > UI > EventSystem

---

## Summary Table

| Script          | Type             | Purpose                                  |
| --------------- | ---------------- | ---------------------------------------- |
| BuildingData    | ScriptableObject | Configuration for building types         |
| BuildingItem    | MonoBehaviour    | Component on placed buildings for refund |
| InventoryButton | MonoBehaviour    | UI button to select buildings            |
| Draggable       | MonoBehaviour    | Drag-and-drop with grid snapping         |
| DeleteManager   | MonoBehaviour    | Deletion confirmation and refund         |
| VillageManager  | MonoBehaviour    | Main controller coordinating all systems |

**Data flow:**

```
BuildingData (assets)
       ↓
VillageManager.PopulateInventory()
       ↓
InventoryButton (UI)
       ↓
VillageManager.SpawnBuildingFromData()
       ↓
Building prefab + BuildingItem + Draggable
       ↓
OnPlacementConfirmed() → SaveManager
       ↓
Click placed building → DeleteManager → Refund + Destroy
```
